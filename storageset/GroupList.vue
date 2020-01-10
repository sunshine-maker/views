<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="卷组名称">
              <a-input v-model="queryParam.id" placeholder=""/>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="卷组状态">
              <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                <a-select-option value="0">在线</a-select-option>
                <a-select-option value="1">降级</a-select-option>
                <a-select-option value="2">故障</a-select-option>
                <a-select-option value="3">未导入</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="!advanced && 8 || 24" :sm="24">
            <span class="table-page-search-submitButtons" :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
              <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
              <a-button style="margin-left: 8px" @click="() => queryParam = {}">重置</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <div class="table-operator">
      <a-button type="primary" icon="plus" @click="() => setNew_Disk(true)">新建卷组</a-button>
      <a-modal
          title="新建卷组(应自动列出同规格可用的磁盘)"
          centered
          v-model="new_disk"
          @ok="() => new_disk = false"
          width = "500"
        >
          <template>
            <div>
              <div style="margin-bottom: 15px;float: left;">
                <div style="float: left;width: 410px;">
                  <a-input placeholder="请输入新卷组名称"/>
                </div>
                <div style="margin-left: 10px; float: left;">
                  <a-select defaultValue="RAID5">
                    <a-select-option value="RAID0">RAID0</a-select-option>
                    <a-select-option value="RAID1">RAID1</a-select-option>
                    <a-select-option value="RAID5">RAID5</a-select-option>
                    <a-select-option value="RAID6">RAID6</a-select-option>
                    <a-select-option value="RAID5+0">RAID5+0</a-select-option>
                    <a-select-option value="RAID6+0">RAID6+0</a-select-option>
                    <a-select-option value="三倍校验">三倍校验</a-select-option>
                  </a-select>
                </div>
              </div>
              
              </br>
              <div style="margin:0 0 15px 0;float: left;">
                <a-alert message="原卷组为Raid5模式,需至少选择3块磁盘!" banner closable />
              </div>
            </div>
              <a-table :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}":columns="add_columns" :dataSource="replace_data"  />
            
          </template>
        </a-modal>
      <a-dropdown >
        <a-menu slot="overlay">
          <a-menu-item key="1"><a-icon type="cloud-upload" />导入卷组配置文件</a-menu-item>
          <!-- lock | unlock -->
          <a-menu-item key="2"><a-icon type="cloud-download" />导出配置文件</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          配置文件 <a-icon type="down" />
        </a-button>
      </a-dropdown>
      <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1"><a-icon type="interation" />清理卷</a-menu-item>
          <!-- lock | unlock -->
          <a-menu-item key="2"><a-icon type="file-search" />检查卷</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作 <a-icon type="down" />
        </a-button>
      </a-dropdown>
    </div>

    <s-table
      ref="table"
      size="default"
      rowKey="key"
      :columns="columns"
      :data="loadData"
      :alert="options.alert"
      :rowSelection="options.rowSelection"
      showPagination="auto"
    >
      <span slot="serial" slot-scope="text, record, index">
        {{ index + 1 }}
      </span>
      <span slot="status" slot-scope="text">
        <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
      </span>
      <span slot="description" slot-scope="text">
        <ellipsis :length="4" tooltip>{{ text }}</ellipsis>
      </span>

      <span slot="action" slot-scope="text, record">
        <template>
          <a-dropdown>
              <a class="ant-dropdown-link" href="#">
                操作 <a-icon type="down" />
              </a>
              <a-menu slot="overlay">
                <a-menu-item @click="() => setRaid_Disk(true)">卷磁盘管理</a-menu-item>
                <a-modal
                    title="XXX卷磁盘管理"
                    centered
                    v-model="raid_disk"
                    @ok="() => raid_disk = false"
                    width = "500"
                  >
                   <template>
                     <a-table :columns="disk_columns" :dataSource="disk_data">
                       <span slot="status" slot-scope="text">
                         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
                       </span>
                       <span slot="action" slot-scope="text, record">
                         <span v-if="record.disk_type === '数据盘'">
                            <a  @click="() => Replace_Disk(true)">替换</a>
                            <a-modal
                                title="替换XXX磁盘(应自动列出同规格可用的磁盘)"
                                centered
                                v-model="replace_disk"
                                @ok="() => replace_disk = false"
                                width = "500"
                              >
                                <a-table :columns="replace_columns" :dataSource="replace_data">
                                  <span slot="action" slot-scope="text, record">
                                      <span v-if="record.id > 0" >
                                        <a @click="$refs.deleteModal.del()" disabled="true">替换</a>
                                      </span>
                                      <span v-else>
                                        <a-popconfirm title="是否确定使用该硬盘替换已有磁盘？" @confirm="remove(record.id)">
                                          <a>替换</a>
                                        </a-popconfirm>
                                      </span> 
                                  </span>
                                </a-table>
                              </a-modal>
                         </span>
                         <span v-else-if="record.disk_type === '热备盘'">
                           <span v-if="record.id > 5 ">
                              <a @click="$refs.deleteModal.del()">移除</a>
                           </span>
                           <span v-else>
                               <a-popconfirm title="是否要移除此热备盘？" @confirm="remove(record.id)">
                                 <a>移除</a>
                               </a-popconfirm>
                           </span> 
                          </span> 
                           <span v-else-if="record.disk_type === '读缓存'">
                             <span v-if="record.id > 5 ">
                              <a @click="$refs.deleteModal.del()">移除</a>
                             </span>
                             <span v-else>
                                 <a-popconfirm title="是否要移除此读缓存盘？" @confirm="remove(record.id)">
                                   <a>移除</a>
                                 </a-popconfirm>
                             </span> 
                            </span> 
                           <span v-else-if="record.disk_type === '写缓存'">
                             <span v-if="record.id > 5 ">
                                <a @click="$refs.deleteModal.del()">移除</a>
                             </span>
                             <span v-else>
                                 <a-popconfirm title="是否要移除此写缓存盘？" @confirm="remove(record.id)">
                                   <a>移除</a>
                                 </a-popconfirm>
                             </span> 
                           </span> 
                         <span v-if="record.status > 2">
                           <a-divider type="vertical" />
                           <span v-if="record.id < 3" >
                             <a @click="$refs.deleteModal.del()" disabled="true">移除</a>
                           </span>
                           <span v-else>
                             <a-popconfirm title="是否要移除此故障盘？" @confirm="remove(record.id)">
                               <a>移除</a>
                             </a-popconfirm>
                           </span> 
                         </span>
                       </span>
                     </a-table>
                   </template>
                  </a-modal>
                <a-sub-menu title="添加功能盘" key="test">
                  <a-menu-item @click="() => Add_Hotdisk(true)">添加热备盘</a-menu-item>
                  <a-modal
                      title="添加热备盘(应自动列出同规格可用的磁盘)"
                      centered
                      v-model="add_hotdisk"
                      @ok="() => add_hotdisk = false"
                      width = "500"
                    >
                      <template>
                        <a-table :columns="replace_columns" :dataSource="replace_data">
                          <span slot="action" slot-scope="text, record">
                              <span v-if="record.id > 0" >
                                <a @click="$refs.deleteModal.del()" disabled="true">添加</a>
                              </span>
                              <span v-else>
                                <a-popconfirm title="是否确定使用该硬盘作为热备盘？" @confirm="remove(record.id)">
                                  <a>添加</a>
                                </a-popconfirm>
                              </span> 
                          </span>
                        </a-table>
                      </template>
                    </a-modal>
                  <a-menu-item  @click="() => setRead_Cache(true)">添加读缓存</a-menu-item>
                  <a-modal
                      title="添加读缓存(应自动列出同规格可用的磁盘)"
                      centered
                      v-model="read_cache"
                      @ok="() => read_cache = false"
                      width = "500"
                    >
                      <template>
                        <a-table :columns="replace_columns" :dataSource="cache_data">
                          <span slot="action" slot-scope="text, record">
                              <span v-if="record.id > 0" >
                                <a @click="$refs.deleteModal.del()" disabled="true">添加</a>
                              </span>
                              <span v-else>
                                <a-popconfirm title="是否确定使用该硬盘作为读缓存？" @confirm="remove(record.id)">
                                  <a>添加</a>
                                </a-popconfirm>
                              </span> 
                          </span>
                        </a-table>
                      </template>
                    </a-modal>
                  <a-menu-item  @click="() => setWrite_Cache(true)">添加写缓存</a-menu-item>
                  <a-modal
                      title="添加写缓存(应自动列出同规格可用的磁盘)"
                      centered
                      v-model="write_cache"
                      @ok="() => write_cache = false"
                      width = "500"
                    >
                      <template>
                        <a-table :columns="replace_columns" :dataSource="cache_data">
                          <span slot="action" slot-scope="text, record">
                              <span v-if="record.id > 0" >
                                <a @click="$refs.deleteModal.del()" disabled="true">添加</a>
                              </span>
                              <span v-else>
                                <a-popconfirm title="是否确定使用该硬盘作为写缓存？" @confirm="remove(record.id)">
                                  <a>添加</a>
                                </a-popconfirm>
                              </span> 
                          </span>
                        </a-table>
                      </template>
                    </a-modal>
                </a-sub-menu>
                <a-menu-item  @click="() => setAdd_Disk(true)">扩容卷组</a-menu-item>
                <a-menu-item>
                <span v-if="record.id < 3" >
                  <a @click="$refs.deleteModal.del()" disabled="true">删除卷组</a>
                </span>
                <span v-else>
                    <a-popconfirm title="是否要删除卷组此卷组？" @confirm="remove(record.id)">
                      <span>删除卷组</span>
                    </a-popconfirm>
                </span> 
                </a-menu-item>
                <a-menu-item>
                <span v-if="record.status < 1" >
                  <span v-if="record.status === 0">
                     <a @click="$refs.deleteModal.del()">导入卷组</a>
                  </span>
                  <span v-else>
                      <a-popconfirm title="是否导入卷组此卷组？" @confirm="remove(record.id)">
                        <span>导入卷组</span>
                      </a-popconfirm>
                  </span> 
                </span>
                <span v-else>
                  <a @click="handleEdit(record)" disabled="true">导入卷组</a>
                </span>
                </a-menu-item>
                 <a-modal
                     title="扩容XXX卷组(应自动列出同规格可用的磁盘)"
                     centered
                     v-model="add_disk"
                     @ok="() => add_disk = false"
                     width = "500"
                   >
                     <template>
                       <div>
                         <a-alert message="原卷组为Raid5模式,需至少选择3块磁盘!" banner closable />
                       </div>
                       <a-table :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}":columns="add_columns" :dataSource="cache_data"  />
                     </template>
                   </a-modal>
              </a-menu>
            </a-dropdown>
            
        </template>
      </span>
    </s-table>
    <create-form ref="createModal" @ok="handleOk" />
    <step-by-step-modal ref="modal" @ok="handleOk"/>
  </a-card>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
// import StepByStepModal from './modules/StepByStepModal'
// import CreateForm from './modules/CreateForm'
import { getRoleList, getServiceList} from '@/api/manage'
const statusMap = {
  0: {
    status: 'default',
    text: '未导入卷组'
  },
  1: {
    status: 'success',
    text: '在线'
  },
  2: {
    status: 'processing',
    text: '降级'
  },
  3: {
    status: 'error',
    text: '故障'
  }
}
export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis
    // CreateForm,
    // StepByStepModal
  },
  data () {
    return {
      read_cache: false,
      raid_disk: false,
      write_cache: false,
      replace_disk:false,
      add_hotdisk:false,
      add_disk:false,
      new_disk:false,
      modify_group: false,
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      disk_columns:[
        {
          title: '状态',
          dataIndex: 'status',
          scopedSlots: { customRender: 'status' }
        },
        {
          title: '类别',
          dataIndex: 'disk_type'
        },
        {
          title: '磁盘名',
          dataIndex: 'disk_name'
        },
        {
          title: '位置',
          dataIndex: 'disk_loc'
        },
        {
          title: '容量',
          dataIndex: 'disk_vol'
        },
        {
          title: '接口',
          dataIndex: 'disk_inte'
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '150px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      replace_columns:[
        {
          title: '磁盘名',
          dataIndex: 'disk_name'
        },
        {
          title: '位置',
          dataIndex: 'disk_loc'
        },
        {
          title: '容量',
          dataIndex: 'disk_vol'
        },
        {
          title: '接口',
          dataIndex: 'disk_inte'
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '60px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      add_columns:[
        {
          title: '磁盘名',
          dataIndex: 'disk_name',
        },
        {
          title: '位置',
          dataIndex: 'disk_loc',
        },
        {
          title: '容量',
          dataIndex: 'disk_vol',
        },
        {
          title: '接口',
          dataIndex: 'disk_inte',
        }
      ],
      replace_data:[
        {
          key: '1',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0tAD00000500D60000d0',
          disk_loc: 'host-1-1'
        },
        {
          key: '2',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t50F000000C621972d0',
          disk_loc: 'Host-1-2'
        },
        {
          key: '3',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t500C500129730ABd0',
          disk_loc: 'JBod-1-1'
        },
        {
          key: '4',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t5000CCA224D3FF2Ed0',
          disk_loc: 'JBod-1-2'
        },
        {
          key: '5',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-1'
        }
      ],
      cache_data:[
        {
          key: '1',
          disk_inte:'SSD',
          disk_vol:'480GB',
          disk_name: 'c0tAD00000500D60000d0',
          disk_loc: 'host-1-1'
        },
        {
          key: '2',
          disk_inte:'SSD',
          disk_vol:'480GB',
          disk_name: 'c0t50F000000C621972d0',
          disk_loc: 'Host-1-2'
        },
        {
          key: '3',
          disk_inte:'SSD',
          disk_vol:'480GB',
          disk_name: 'c0t500C500129730ABd0',
          disk_loc: 'JBod-1-1'
        },
        {
          key: '4',
          disk_inte:'SSD',
          disk_vol:'480GB',
          disk_name: 'c0t5000CCA224D3FF2Ed0',
          disk_loc: 'JBod-1-2'
        },
        {
          key: '5',
          disk_inte:'SSD',
          disk_vol:'480GB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-1'
        }
      ],
      disk_data:[
        {
          key: '1',
          status: '3',
          disk_type:'数据盘',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0tAD00000500D60000d0',
          disk_loc: 'host-1-1'
        },
        {
          key: '2',
          status: '1',
          disk_type:'数据盘',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t50F000000C621972d0',
          disk_loc: 'Host-1-2'
        },
        {
          key: '3',
          status: '1',
          disk_type:'数据盘',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t500C500129730ABd0',
          disk_loc: 'JBod-1-1'
        },
        {
          key: '4',
          status: '1',
          disk_type:'数据盘',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t5000CCA224D3FF2Ed0',
          disk_loc: 'JBod-1-2'
        },
        {
          key: '5',
          status: '1',
          disk_type:'热备盘',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-1'
        },
        {
          key: '6',
          status: '1',
          disk_type:'读缓存',
          disk_inte:'SSD',
          disk_vol:'480GB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-1'
        },
        {
          key: '7',
          status: '1',
          disk_type:'写缓存',
          disk_inte:'SSD',
          disk_vol:'480GB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-1'
        }
      ],
      columns: [
        {
          title: 'ID',
          scopedSlots: { customRender: 'serial' }
        },
        {
          title: '存储卷名称',
          dataIndex: 'no'
        },
        {
          title: 'RAID组态',
          dataIndex: 'description',
          scopedSlots: { customRender: 'description' }
        },
        {
          title: '总空间',
          dataIndex: 'callNo',
          sorter: true,
          needTotal: true,
          customRender: (text) => text + ' TB'
        },
        {
          title: '状态',
          dataIndex: 'status',
          scopedSlots: { customRender: 'status' }
        },
        {
          title: '已用空间',
          dataIndex: 'updatedAt',
          sorter: true
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        console.log('loadData.parameter', parameter)
        return getServiceList(Object.assign(parameter, this.queryParam))
          .then(res => {
            return res.result
          })
      },
      selectedRowKeys: [],
      selectedRows: [],

      // custom table alert & rowSelection
      options: {
        alert: { show: false, clear: () => { this.selectedRowKeys = [] } },
        rowSelection: {
          selectedRowKeys: this.selectedRowKeys,
          onChange: this.onSelectChange
        }
      },
      optionAlertShow: false
    }
  },
  filters: {
    statusFilter (type) {
      return statusMap[type].text
    },
    statusTypeFilter (type) {
      return statusMap[type].status
    }
  },
  created () {
    this.tableOption()
    getRoleList({ t: new Date() })
  },
  computed: {
    hasSelected() {
      return this.selectedRowKeys.length > 0
    }
  },
  methods: {
    modify_Group(modify_group){
      this.modify_group = modify_group;
    },
    setModal1Visible(modal1Visible) {
          this.modal1Visible = modal1Visible;
        },
    setRaid_Disk(raid_disk) {
             this.raid_disk = raid_disk;
           },
    setModal3Visible(modal3Visible) {
          this.modal3Visible = modal3Visible;
        },
    Replace_Disk(replace_disk){
      this.replace_disk = replace_disk;
    },
    setRead_Cache(read_cache) {
          this.read_cache = read_cache;
        },
    setWrite_Cache(write_cache) {
          this.write_cache = write_cache;
        },
    Add_Hotdisk(add_hotdisk){
      this.add_hotdisk = add_hotdisk;
    },
    setAdd_Disk(add_disk){
      this.add_disk = add_disk;
    },
    setNew_Disk(new_disk){
      this.new_disk = new_disk},
    onSelectChange (selectedRowKeys) {
          console.log('selectedRowKeys changed: ', selectedRowKeys);
          this.selectedRowKeys = selectedRowKeys
        },
    tableOption () {
      if (!this.optionAlertShow) {
        this.options = {
          alert: { show: false, clear: () => { this.selectedRowKeys = [] } },
          rowSelection: {
            selectedRowKeys: this.selectedRowKeys,
            onChange: this.onSelectChange,
            getCheckboxProps: record => ({
              props: {
                disabled: record.no === 'NO 2', // Column configuration not to be checked
                name: record.no
              }
            })
          }
        }
        this.optionAlertShow = false
      } else {
        this.options = {
          alert: false,
          rowSelection: null
        }
        this.optionAlertShow = false
      }
    },

    handleEdit (record) {
      console.log(record)
      this.$refs.modal.edit(record)
    },
    remove (key) {
      const newData = this.data.filter(item => item.key !== key)
      this.data = newData
    },
    handleSub (record) {
      if (record.status !== 0) {
        this.$message.info(`${record.no} 订阅成功`)
      } else {
        this.$message.error(`${record.no} 订阅失败，规则已关闭`)
      }
    },
    handleOk () {
      this.$refs.table.refresh()
    },
    onSelectChange (selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    toggleAdvanced () {
      this.advanced = !this.advanced
    },
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    }
  }
}
</script>
