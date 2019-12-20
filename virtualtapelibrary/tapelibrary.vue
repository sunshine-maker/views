<template>
  <a-card :bordered="false">
    <div class="table-operator" style="margin-bottom: 10px;">
      <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0" >
        <a-menu slot="overlay">
          <a-menu-item key="1">
            <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
              <template slot="title">
                <p>是否确定批量删除所有已选节点？</p>
                <a-checkbox>确认修改</a-checkbox>
              </template>
              <a-icon type="delete"/> <a>批量删除</a>
            </a-popconfirm>
          </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作 <a-icon type="down" />
        </a-button>
      </a-dropdown>
    
    </div>
    <div style="margin-bottom: 10px;">
    <a-alert message="********" banner closable />
    </div>
   <template>
     <a-table :columns="columns" :dataSource="data" class="components-table-demo-nested">
       <span slot="mapping" slot-scope="mapping, record">
           <a-tag
             v-for="tag in mapping"
             :color="tag==='loser' ? 'volcano' : (tag.length > 5 ? 'geekblue' : 'green')"
             :key="tag"
           >
             {{tag.toUpperCase()}}
           </a-tag>
       </span>
       <span slot="action" slot-scope="text, record">
         <a-dropdown :trigger="['click']">
             <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
             <a-menu slot="overlay">
               <a-menu-item key="0">
                  <a @click="() => Tapes(true)"><span>磁带入库</span></a>
               </a-menu-item>
               <a-menu-item key="1">
                  <a @click="() => Tapes(true)"><span>磁带出库</span></a>
               </a-menu-item>
               <a-sub-menu title="卸载磁带" key="2">
                  <a-menu-item>
                    <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                    <template slot="title">
                      <p>是否确定卸载驱动器1中的磁带？</p>
                      <a-checkbox>确认卸载磁带</a-checkbox>
                    </template>
                    <span>卸载驱动器1磁带</span>
                  </a-popconfirm></a>
                  </a-menu-item>
                  <a-menu-item>
                    <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                      <template slot="title">
                        <p>是否确定卸载驱动器2中的磁带？</p>
                        <a-checkbox>确认卸载磁带</a-checkbox>
                      </template>
                      <span>卸载驱动器2磁带</span>
                    </a-popconfirm></a>
                  </a-menu-item>
                  <a-menu-item>
                    <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                      <template slot="title">
                        <p>是否确定卸载所有磁带？</p>
                        <a-checkbox>确认卸载磁带</a-checkbox>
                      </template>
                      <span>卸载全部磁带</span>
                    </a-popconfirm></a>
                  </a-menu-item>
                </a-sub-menu>
                 <a-menu-item key="3">
                    <span v-if="record.address === '未映射'">
                      <span @click="() => set_Address(true)">添加映射</span>
                    </span>
                    <span v-else>
                      <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="() => set_Address(true)">
                        <template slot="title">
                          <p>修改映射将影响当前正在使用该设备的主机及应用系统！</p>
                          <p>是否确认修改该映射？</p>
                          <a-checkbox>确认修改</a-checkbox>
                        </template>
                        <span>修改映射</span>
                      </a-popconfirm>
                    </span>
                    <a-modal
                        title="为磁盘组XXX添加/修改映射"
                        centered
                        v-model="set_address"
                        @ok="() => set_address = false"
                        :width="500"
                        :visible="visible"
                      >
                             
                           <a-form :form="form">
                               <a-form-item>
                                 <a-row>
                                   <a-col :sm="7" :xs="24">
                                       <span>编辑iSCSI映射</span>
                                   </a-col>
                                   <a-col :sm="10" :xs="24">
                                     <a-select
                                           mode="multiple"
                                           :size="size"
                                           placeholder="Please select"
                                           :defaultValue="['a1', 'b2']"
                                           style="width: 200px"
                                           @change="handleChange"
                                           @popupScroll="popupScroll"
                                         >
                                           <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                                             {{(i + 9).toString(36) + i}}
                                           </a-select-option>
                                         </a-select>
                                   </a-col>  
                                 </a-row>
                             </a-form-item>
                             <a-form-item>
                                 <a-row>
                                   <a-col :sm="7" :xs="24">
                                       <span>编辑FC映射</span>
                                   </a-col>
                                   <a-col :sm="10" :xs="24">
                                     <a-select
                                           mode="multiple"
                                           :size="size"
                                           placeholder="Please select"
                                           :defaultValue="['a1', 'b2']"
                                           style="width: 200px"
                                           @change="handleChange"
                                           @popupScroll="popupScroll"
                                         >
                                           <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                                             {{(i + 9).toString(36) + i}}
                                           </a-select-option>
                                         </a-select>
                                   </a-col>  
                                 </a-row>
                             </a-form-item>
                             <a-form-item>
                                 <a-row>
                                   <a-col :sm="7" :xs="24">
                                       <span>编辑IB映射</span>
                                   </a-col>
                                   <a-col :sm="10" :xs="24">
                                     <a-select
                                           mode="multiple"
                                           :size="size"
                                           placeholder="Please select"
                                           :defaultValue="['a1', 'b2']"
                                           style="width: 200px"
                                           @change="handleChange"
                                           @popupScroll="popupScroll"
                                         >
                                           <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                                             {{(i + 9).toString(36) + i}}
                                           </a-select-option>
                                         </a-select>
                                   </a-col>  
                                 </a-row>
                             </a-form-item>
                           </a-form>
                      </a-modal>
                 </a-menu-item>
                 <a-menu-item key="4">
                  <a @click="() => add_Member(true)"><span>修改参数</span></a>
               </a-menu-item>
               <a-menu-item key="5">
                  <a @click="() => show_Pro(true)"><span>查看详情</span></a>
               </a-menu-item>
               <a-menu-item key="1">
                 <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                <template slot="title">
                  <p>是否确定删除该磁带库？</p>
                  <a-checkbox>确认删除</a-checkbox>
                </template>
                <span>删除</span>
              </a-popconfirm></a>
               </a-menu-item>
             </a-menu>
           </a-dropdown>
           <a-modal
               title="XXX磁带出/入库"
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
                             <span>选择带架</span>
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
    text: '已关闭'
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
      add_member: false,
      set_address: false,
      tapes: false,
      show_pro: false,
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
      pro_columns: [
        {
          title: '类别',
          dataIndex: 'type',
          width: '120px',
        },
        {
          title: '信息',
          dataIndex: 'info',
          width: '220px',
        },
        {
          title: '详情',
          dataIndex: 'other',
          scopedSlots: { customRender: 'other' },
        }
      ],
      pro_data: [
        {
          type: '磁带库编号',
          info: '1',
        },
        {
          type: '序列号',
          info: '00005df1ead7001e',
        },
        {
          type: 'GUID',
          info: '6920613069464b0000005df1ead7001e',
        },
        {
          type: '厂商',
          info: 'ADIC',
        },
        {
          type: '产品名',
          info: 'Scalar 24',
        },
        {
          type: '产品版本号',
          info: '2.78',
        },
        {
          type: '槽位数',
          info: '20',
        },
        {
          type: '驱动器数',
          info: '2',
          other:  ['1-TAPE100000', '2-TAPE100002'],
        },
        {
          type: '驱动器厂商',
          info: 'IBM',
        },
        {
          type: '驱动器产品名',
          info: 'ULTRIUM-TD1',
        },
        {
          type: '驱动器版本',
          info: '27Q1',
        },
        {
          type: '已加载磁带',
          info: '3',
          other: ['TAPE100000', 'TAPE100001', 'TAPE100002'],
        }
      ],
      incolumns: [
        {
          title: '成员iqn',
          dataIndex: 'iqn',
        },
        {
          title: 'CHAP验证',
          dataIndex: 'chap',
        },
        {
          title: '状态',
          dataIndex: 'status',
           scopedSlots: { customRender: 'status' }
        },
         {
           title: '操作',
           dataIndex: 'action',
           width: '100px',
           scopedSlots: { customRender: 'action' }
         }
      ],
      indata: [
        {
          iqn: 'iqn.2019-11.com.marstor:1574909220779',
          chap: '已关闭',
          status: '0'
        },
        {
          iqn: 'iqn.2019-11.com.marstor:1574909220779',
          chap: '已关闭',
          status: '4'
        },
        {
          iqn: 'iqn.2019-11.com.marstor:1574909220779',
          chap: '已开启',
          status: '4'
        }
      ],
     
      
      columns: [
         {
           title: '磁带库名称',
           dataIndex: 'tapename',
         },
         {
           title: '驱动器数量',
           dataIndex: 'dri_num'
         },
         {
           title: '出入槽位数',
           dataIndex: 'io_num'
         },
         {
           title: '槽位总数',
           dataIndex: 'holder_num'
         },
         {
           title: '现有磁带数',
           dataIndex: 'tape_num'
         },
         {
           title: '映射',
           dataIndex: 'mapping',
           scopedSlots: { customRender: 'mapping' }
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
           tapename: 'tap_001',
           dri_num: '2',
           io_num: '2',
           holder_num: '20',
           tape_num: '3',
           mapping: ['iSCSI_2','iSCSI_3']
         }
       ],
       set_Address(set_address){
         this.set_address = set_address;
       },
       Tapes(tapes){
         this.tapes = tapes;
       },
       show_Pro(show_pro){
         this.show_pro = show_pro;
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
              console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
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
