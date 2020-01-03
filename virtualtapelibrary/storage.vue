<template>
  <a-card :bordered="false">
    <div class="table-operator" style="margin-bottom: 10px;">
      <a-button type="primary" icon="plus" @click="() => add_Group(true)">新建磁带架</a-button>
      <a-modal
         title="新建磁带架"
         centered
         v-model="add_group"
         @ok="() => add_group = false"
          :width="500"
          :visible="visible"
        >
        <a-form :form="form">
          <a-form-item>
            <a-row>
              <a-col :sm="7" :xs="24">
                <span>磁带架名称</span>
              </a-col>
              <a-col :sm="10" :xs="24">
                <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入主机组名称'}]}]" />
              </a-col>  
            </a-row>
          </a-form-item>
        </a-form>
      </a-modal>
    </div>
    <div style="margin-bottom: 10px;">
    <a-alert message="********" banner closable />
    </div>
    <template>
      <a-table :columns="columns" :dataSource="data">
        <span slot="status" slot-scope="text">
          <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
        </span>
        <span slot="action" slot-scope="text, record">
          <a-dropdown :trigger="['click']">
            <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
            <a-menu slot="overlay">
              <a-menu-item key="0">
                <a @click="() => mv_Tapes(true)"><span>移动磁带</span></a>
              </a-menu-item>
              <a-menu-item key="1">
                <a @click="() => Tapes(true)"><span>磁带入库</span></a>
                <a-modal
                      title="磁带入库"
                      centered
                      v-model="tapes"
                      @ok="() => tapes = false"
                      :width="700"
                      :visible="visible"
                      :confirmLoading="confirmLoading"
                   >
                   <a-spin :spinning="confirmLoading">
                     <a-form :form="form">
                       <a-form-item>
                         <a-row>
                           <a-col :sm="4" :xs="24">
                             <span>剩余槽位</span>
                           </a-col>
                           <a-col :sm="6" :xs="24">
                             <span>17</span>
                           </a-col>  
                         </a-row>
                       </a-form-item>
                       <a-form-item>
                         <a-row>
                           <a-col :sm="4" :xs="24">
                             <span>选择磁带库</span>
                           </a-col>
                           <a-col :sm="10" :xs="24">
                             <a-select defaultValue="tttt" >
                               <a-select-option value="tttt">tttt</a-select-option>
                             </a-select>
                           </a-col>  
                         </a-row>
                       </a-form-item>
                       <a-form-item>
                         <a-table :columns="in_columns" :dataSource="in_data" :rowSelection="rowSelection"/>
                       </a-form-item>
                     </a-form>
                   </a-spin>
                </a-modal>
              </a-menu-item>
              <a-menu-item key="2">
                <a @click="() => list_Tapes(true)"><span>磁带清单</span></a>
                <a-modal
                      title="磁带清单"
                      centered
                      v-model="list_tapes"
                      @ok="() => list_tapes = false"
                      :width="500"
                      :visible="visible"
                      :confirmLoading="confirmLoading"
                   >
                  <a-spin :spinning="confirmLoading">
                    <a-form :form="form">
                      <a-form-item>
                        <a-table :columns="in_columns" :dataSource="in_data" />
                      </a-form-item>
                    </a-form>
                  </a-spin>
                </a-modal>
              </a-menu-item>
              <a-menu-item key="3">
                <a @click="() => por_Config(true)"><span>存储属性</span></a>
                <a-modal
                      title="存储属性"
                      centered
                      v-model="por_config"
                      @ok="() => por_config = false"
                      :width="700"
                      :visible="visible"
                      :confirmLoading="confirmLoading"
                   >
                   <a-spin :spinning="confirmLoading">
                     <a-form-item>
                         <a-row>
                           <a-col :sm="8" :xs="24">
                             <a-checkbox>启用重复数据删除</a-checkbox>
                           </a-col>
                           <a-col :sm="8" :xs="24">
                             <a-checkbox>启用数据校验</a-checkbox>
                           </a-col>
                         </a-row>
                       </a-form-item>
                       <a-form-item>
                         <a-row>
                           <a-col :sm="8" :xs="24">
                             <a-checkbox>启用数据压缩</a-checkbox>
                           </a-col>
                           <a-col :sm="8" :xs="24">
                             <a-select defaultValue="高效" style="width: 120px" @change="handleChange">
                               <a-select-option value="中等">中等</a-select-option>
                               <a-select-option value="高等">高等</a-select-option>
                               <a-select-option value="高效">高效</a-select-option>
                             </a-select>
                           </a-col>
                         </a-row>
                       </a-form-item>
                       <a-form-item>
                         <a-row>
                           <a-col :sm="8" :xs="24">
                             <a-checkbox>启用配额管理</a-checkbox>
                           </a-col>
                           <!-- <a-col :sm="7" :xs="24">
                             <span>最大分配空间</span>
                           </a-col> -->
                           <a-col :sm="10" :xs="24">
                             <div style="float:left">
                             <a-input-number
                               :defaultValue="100"
                               :min="0"
                               :max="100000"
                               :formatter="value => `${value}GB`"
                               :parser="value => value.replace('GB', '')"
                             />
                             </div>
                           <div style="width:15px;height:15px;margin-left: 15px;float:left;">
                             <a-popover placement="right">
                                 <template slot="content">
                                   <span>不得小于4GB</span>
                                 </template>
                                 <a-button shape="circle" icon="question-circle" :size="small"/>
                               </a-popover>
                            </div>
                            </a-col>
                         </a-row>
                       </a-form-item>
                       <a-form-item>
                         <a-row>
                           <a-col :sm="8" :xs="24">
                             <span>块大小</span>
                           </a-col>
                           <a-col :sm="8" :xs="24">
                             <a-select defaultValue="128KB" style="width: 120px" @change="handleChange">
                               <a-select-option value="4">4KB</a-select-option>
                               <a-select-option value="8">8KB</a-select-option>
                               <a-select-option value="16">16KB</a-select-option>
                               <a-select-option value="32">32KB</a-select-option>
                               <a-select-option value="64">64KB</a-select-option>
                               <a-select-option value="128">128KB</a-select-option>
                             </a-select>
                           </a-col>
                         </a-row>
                       </a-form-item>
                       <a-form-item>
                         <a-row>
                           <a-col :sm="8" :xs="24">
                             <span>同步写入</span>
                           </a-col>
                           <a-col :sm="8" :xs="24">
                             <a-select defaultValue="标准" style="width: 120px" @change="handleChange">
                               <a-select-option value="总是">总是</a-select-option>
                               <a-select-option value="标准">标准</a-select-option>
                               <a-select-option value="关闭">关闭</a-select-option>
                             </a-select>
                           </a-col>
                         </a-row>
                       </a-form-item>
                       <a-form-item>
                         <a-row>
                           <a-col :sm="8" :xs="24">
                             <a-checkbox>授权用户</a-checkbox>
                           </a-col>
                           <a-col :sm="16" :xs="24">
                             <a-select style="width: 120px" @change="handleChange" />
                           </a-col>
                         </a-row>
                       </a-form-item>
                     </a-form>   
                   </a-spin>
                </a-modal>
              </a-menu-item>
              <a-sub-menu title="快照保护" key="4">
                 <a-menu-item>
                   <a @click="() => list_Snapshot(true)"><span>快照清单</span></a>
                   <a-modal
                         title="快照清单"
                         centered
                         v-model="list_snapshot"
                         @ok="() => list_snapshot = false"
                         :width="700"
                         :visible="visible"
                         :confirmLoading="confirmLoading"
                      >
                        
                      <a-spin :spinning="confirmLoading">
                        <a-form :form="form">
                          <a-form-item>
                            <a-row :gutter="48">
                              <a-col :sm="4" :xs="24">
                                <a-dropdown>
                                  <a-menu slot="overlay">
                                    <a-menu-item key="1">
                                      <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                        <template slot="title">
                                          <p>是否确定批量删除所有已选快照？</p>
                                          <a-checkbox>确认删除</a-checkbox>
                                        </template>
                                        <a-icon type="delete"/> <a>批量删除</a>
                                      </a-popconfirm>
                                    </a-menu-item>
                                  </a-menu>
                                  <a-button style="margin-left: 8px">
                                    批量操作 <a-icon type="down" />
                                  </a-button>
                                </a-dropdown>
                              </a-col>
                              <a-col :sm="6" :xs="24">
                                <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                  <template slot="title">
                                    <p>是否确定手动创建快照？</p>
                                    <a-checkbox>确认创建</a-checkbox>
                                  </template>
                                  <a-button>创建快照</a-button>
                                </a-popconfirm>
                              </a-col>
                            </a-row>
                          </a-form-item>
                          <a-form-item>
                            
                            <a-table :columns="sp_columns" :dataSource="sp_data" :rowSelection="rowSelection">
                              <span slot="action" slot-scope="text, record">
                                <a-dropdown :trigger="['click']">
                                  <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
                                  <a-menu slot="overlay">
                                    <a-menu-item key="0">
                                      <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                        <template slot="title">
                                          <p>是否确定回滚选中快照？</p>
                                          <a-checkbox>确认回滚</a-checkbox>
                                        </template>
                                        <span>回滚快照</span>
                                      </a-popconfirm>
                                    </a-menu-item>
                                    <a-menu-item key="0">
                                      <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                        <template slot="title">
                                          <p>是否确定删除选中快照？</p>
                                          <a-checkbox>确认删除</a-checkbox>
                                        </template>
                                        <span>删除快照</span>
                                      </a-popconfirm>
                                    </a-menu-item>
                                  </a-menu>
                                </a-dropdown>
                              </span>
                            </a-table>
                          </a-form-item>
                        </a-form>   
                      </a-spin>
                   </a-modal>
                 </a-menu-item>
                 <a-menu-item>
                   <a @click="() => auto_Snapshot(true)"><span>自动快照</span></a>
                   <a-modal
                         title="设置XXX存储空间自动快照"
                         centered
                         v-model="auto_snapshot"
                         @ok="() => auto_snapshot = false"
                         :width="400"
                         :visible="visible"
                         :confirmLoading="confirmLoading"
                      >
                      <a-spin :spinning="confirmLoading">
                        <a-form :form="form">
                          <a-form-item>
                            <a-row>
                              <a-switch checkedChildren="开" unCheckedChildren="关" defaultChecked />
                              <span>自动快照</span>
                            </a-row>
                            <a-row>
                              <a-col :sm="7" :xs="24">
                                <span>快照间隔</span>
                              </a-col>
                              <a-col :sm="10" :xs="24">
                                <a-input-number
                                      :defaultValue="100"
                                      :min="0"
                                      :max="10000"
                                      :formatter="value => `${value}分钟`"
                                      :parser="value => value.replace('分钟', '')"
                                      @change="onChange"
                                    />
                              </a-col>  
                            </a-row>
                            
                            <a-row>
                              <a-col :sm="7" :xs="24">
                                <span>保留个数</span>
                              </a-col>
                              <a-col :sm="10" :xs="24">
                                <a-input-number
                                      :defaultValue="100"
                                      :min="0"
                                      :max="10000"
                                      :formatter="value => `${value}个`"
                                      :parser="value => value.replace('个', '')"
                                      @change="onChange"
                                    />
                              </a-col>  
                            </a-row>
                          </a-form-item>
                        </a-form>   
                      </a-spin>
                   </a-modal>
                 </a-menu-item>
               </a-sub-menu>
              <a-menu-item key="5">
                <span v-if ="record.status === '4'">
                  <a-popconfirm placement="left" okText="Yes" cancelText="No">
                    <template slot="title">
                      <p>是否确认上线该存储空间？</p>
                      <a-checkbox>确认上线该存储空间</a-checkbox>
                    </template>
                    <span>空间上线</span>
                  </a-popconfirm></a>
                </span>
                <span v-else>
                  <a-popconfirm placement="left" okText="Yes" cancelText="No">
                    <template slot="title">
                      <p>是否确认离线该存储空间？</p>
                      <a-checkbox>确认离线该存储空间</a-checkbox>
                    </template>
                    <span>空间离线</span>
                  </a-popconfirm></a>
                </span>
              </a-menu-item>
              <a-menu-item key="6">
                <span v-if ="record.key < '2'">
                  <a disabled="true">删除磁带架</a>
                </span>
                <span v-else>
                    <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                      <template slot="title">
                        <p>是否确定删除磁带架？</p>
                        <a-checkbox>确认删除磁带架</a-checkbox>
                      </template>
                      <span>删除磁带架</span>
                    </a-popconfirm></a>
                </span>
              </a-menu-item>
              <a-modal
                    title="移动XXX磁带架上的磁带"
                    centered
                    v-model="mv_tapes"
                    @ok="() => mv_tapes = false"
                    :width="700"
                    :visible="visible"
                    :confirmLoading="confirmLoading"
                 >
                 <a-spin :spinning="confirmLoading">
                   <a-form :form="form">
                     <a-form-item>
                       <a-row>
                         <a-col :sm="4" :xs="24">
                           <span>目标磁带架</span>
                         </a-col>
                         <a-col :sm="10" :xs="24">
                           <a-select defaultValue="空白带架" >
                             <a-select-option value="空白带架">空白带架</a-select-option>
                             <a-select-option value="离线带架">离线带架</a-select-option>
                             <a-select-option value="tttt">tttt</a-select-option>
                           </a-select>
                         </a-col>  
                       </a-row>
                     </a-form-item>
                     <a-form-item>
                       <a-row>
                         <a-col :sm="30" :xs="24">
                           <a-transfer
                               :dataSource="mockData"
                               :listStyle="{
                                 width: '300px',
                                 height: '300px',
                               }"
                               :targetKeys="targetKeys"
                               @change="handleChange"
                               :render="renderItem"
                             >
                           </a-transfer>
                         </a-col>  
                       </a-row>
                     </a-form-item>
                   </a-form>
                 </a-spin>
              </a-modal>
            </a-menu>
          </a-dropdown>
        </span>
        <a-modal
             title="修改XXX磁带库属性"
             centered
             v-model="add_member"
             @ok="() => add_member = false"
             :width="500"
             :visible="visible"
             :confirmLoading="confirmLoading"
           >
              <a-spin :spinning="confirmLoading">
                <a-form :form="form">
                  <a-form-item
                    label="驱动器数"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <a-input/>
                </a-form-item>
                  <a-form-item
                    label="出入槽位数"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <a-input/>
                </a-form-item> 
                     <a-form-item
                       label="槽位数"
                       :labelCol="labelCol"
                       :wrapperCol="wrapperCol"
                     >
                     <a-input/>
                   </a-form-item>
                </a-form>
              </a-spin>
           </a-modal>
           <a-modal
               title="XXX磁带库详细信息"
               centered
               v-model="show_pro"
               @ok="() => show_pro = false"
               :width="700"
               :visible="visible"
               :confirmLoading="confirmLoading"
             >
                <a-spin :spinning="confirmLoading">
                  <a-form :form="form">
                    <a-form-item>
                      <a-table :columns="pro_columns" :dataSource="pro_data" :pagination="false" class="components-table-demo-nested">
                         <span slot="other" slot-scope="tags">
                              <a-tag
                                v-for="tag in tags"
                                :color="tag==='loser' ? 'volcano' : (tag.length > 5 ? 'geekblue' : 'green')"
                                :key="tag"
                              >
                                {{tag.toUpperCase()}}
                              </a-tag>
                            </span>
                      </a-table>
                      </a-form-item>
                  </a-form>
                </a-spin>
             </a-modal>
       </span>
     </a-table>
   </template>
  </a-card>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getRoleList, getServiceList} from '@/api/manage'
const statusMap = {
  
  0: {
    status: 'success',
    text: '已连接'
  },
  1: {
    status: 'error',
    text: '故障'
  },
  2: {
    status: 'processing',
    text: '断开'
  },
  4: {
    status: 'default',
    text: '已离线'
  },
}
export default {
  name: 'IPv4List',
  components: {
    STable,
    Ellipsis
  },
  
  data () {
    return {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 }
      },
      visible: false,
      confirmLoading: false,
      form: this.$form.createForm(this),
      add_group: false,
      list_tapes: false,
      tapes: false,
      mv_tapes: false,
      por_config: false,
      list_snapshot: false,
      auto_snapshot: false,
      selectedRowKeys: [],
      selectedRows: [],
      openKeys: [],
      selectedKeys: [],
      mockData: [],
      targetKeys: [],
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      sp_columns: [
        {
          title: '描述',
          dataIndex: 'describe',
        },
        {
          title: '创建时间',
          dataIndex: 'create_time',
        },
        {
          title: '变量大小',
          dataIndex: 'size',
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      sp_data: [
        {
          describe: '自动',
          create_time: '2019-12-19 14:18:43',
          size: '1.55MB'
        },
        {
          describe: 'test1',
          create_time: '2019-12-19 11:16:27',
          size: '4.76MB'
        }
      ],
      in_columns: [
        {
          title: '磁带条码',
          dataIndex: 'tapenum',
        },
        {
          title: '总容量',
          dataIndex: 'tapecap',
        },
        {
          title: '已用空间',
          dataIndex: 'tapeused',
        },
      ],
      in_data: [
        {
          key: '0',
          tapenum: 'TAPE100000',
          tapecap: '10.0GB',
          tapeused: '2.03MB'
        },
        {
          key: '1',
          tapenum: 'TAPE100001',
          tapecap: '10.0GB',
          tapeused: '2.03MB'
        },
        {
          key: '2',
          tapenum: 'TAPE100002',
          tapecap: '10.0GB',
          tapeused: '2.03MB'
        },
      ],
      columns: [
        {
          title: '空间名称',
          dataIndex: 'storage_name',
        },
        {
          title: '状态',
          dataIndex: 'status',
          scopedSlots: { customRender: 'status' }
        },
        {
          title: '可用空间',
          dataIndex: 'storage_cap'
        },
        {
          title: '已用空间',
          dataIndex: 'storage_used'
        },
        {
          title: '空闲磁带',
          dataIndex: 'free_tapes'
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      data: [
        {
          key: '0',
          storage_name: 'SYSVOL/TAPE',
          status: '0',
          storage_cap: '100GB',
          storage_used: '10GB',
          free_tapes: '3'
        },
        {
          key: '0',
          storage_name: 'test/TAPE',
          status: '0',
          storage_cap: '100GB',
          storage_used: '10GB',
          free_tapes: '3'
        },
        {
          key: '0',
          storage_name: '111111/TAPE',
          status: '4',
          storage_cap: '100GB',
          storage_used: '10GB',
          free_tapes: '3'
        },
      ],
       list_Tapes(list_tapes){
         this.list_tapes = list_tapes;
       },
       Tapes(tapes){
         this.tapes = tapes;
       },
       show_Pro(show_pro){
         this.show_pro = show_pro;
       },
       list_Snapshot(list_snapshot){
         this.list_snapshot = list_snapshot;
       },
       auto_Snapshot(auto_snapshot){
         this.auto_snapshot = auto_snapshot;
       },
       por_Config(por_config){
         this.por_config = por_config;
       },
       mv_Tapes(mv_tapes){
         this.mv_tapes = mv_tapes;
       },
      add_Group(add_group){
        this.add_group = add_group},
      add_Member(add_member){
        this.add_member = add_member},
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        console.log('loadData.parameter', parameter)
        return getServiceList(Object.assign(parameter, this.queryParam))
          .then(res => {
            return res.result
          })
      },
      
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
  computed: {
    rowSelection() {
      const { selectedRowKeys } = this;
      return {
        onChange: (selectedRowKeys, selectedRows) => {
          console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows,'selectedRowKeys.length:',selectedRowKeys.length);
        },
        getCheckboxProps: record => ({
          props: {
            disabled: record.name === 'Disabled User', // Column configuration not to be checked
            name: record.name,
          },
        }),
      };
    },
  },
  mounted() {
    this.getMock();
  },
  methods: {
    getMock() {
      const targetKeys = [];
      const mockData = [];
      for (let i = 0; i < 20; i++) {
        const data = {
          key: i.toString(),
          title: `content${i + 1}`,
          description: `description of content${i + 1}`,
          chosen: Math.random() * 2 > 1,
        };
        if (data.chosen) {
          targetKeys.push(data.key);
        }
        mockData.push(data);
      }
      this.mockData = mockData;
      this.targetKeys = targetKeys;
    },
    renderItem(item) {
      const customLabel = (
        <span class="custom-item">
          {item.title} - {item.description}
        </span>
      );
      return {
        label: customLabel, 
        value: item.title, 
      };
    },
    
    handleMenuClick(e) {
          console.log('click', e);
        },
    handleEdit (record) {
      console.log(record)
      this.$refs.modal.edit(record)
    },
    
    remove (key) {
      const newData = this.data.filter(item => item.key !== key)
      this.data = newData
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
    onSelectChange (selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    }
  }
}
</script>
