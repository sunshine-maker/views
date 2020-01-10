<template>
  <a-card :bordered="false">
    <div class="table-operator">
      <a-button type="primary" icon="plus" @click="() => add_Group(true)">添加主机</a-button>
      <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0" >
        <a-menu slot="overlay">
          <a-menu-item key="1">
            <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
              <template slot="title">
                <p>是否确定批量删除所有已选节点？</p>
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
    <a-modal
       title="添加主机"
       centered
       v-model="add_group"
       @ok="() => add_group = false"
        :width="500"
        :visible="visible"
      >
         <a-form :form="form">
           <a-form-item>
             <a-row>
               <a-col :sm="5" :xs="24">
                   <span>ip地址</span>
               </a-col>
               <a-col :sm="7" :xs="24">
                 <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入主机ip地址'}]}]" />
               </a-col>  
             </a-row>
             </a-form-item>
             <a-form-item>
               <a-row>
                 <a-col :sm="5" :xs="24">
                     <span>端口</span>
                 </a-col>
                 <a-col :sm="7" :xs="24">
                   <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入主机端口'}]}]" />
                 </a-col>  
               </a-row>
               </a-form-item>
         </a-form>
    </a-modal>
    </div>
    <div>
    <a-alert message="********" banner closable />
    </div>
   <template>
      <a-table :columns="columns" :dataSource="data" :pagination="false" :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}">
         <span slot="action" slot-scope="text, record">
            <a-dropdown :trigger="['click']">
                <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
                <a-menu slot="overlay">
                  <a-menu-item key="0">
                     <a @click="() => add_Group(true)"><span>修改</span></a>
                  </a-menu-item>
                  <a-menu-item key="1">
                    <a @click="() => setNet_Dns(true)"><a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                   <template slot="title">
                     <p>是否确定删除该主机？</p>
                     <a-checkbox>确认删除</a-checkbox>
                   </template>
                   <span>删除</span>
                 </a-popconfirm></a>
                  </a-menu-item>
                </a-menu>
              </a-dropdown>
            </span>
          <span slot="expandedRowRender" slot-scope="record" style="margin: 0" >
            <a-table :columns="incolumns" :dataSource="indata" :pagination="false">
              <span slot="status" slot-scope="text">
                <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
              </span>
              <span slot="chap" slot-scope="text">
                <a-badge :status="text | chapstatusTypeFilter" :text="text | chapstatusFilter" />
              </span>
              <span slot="action" slot-scope="text, record">
                <span v-if="record.status ==='1'">
                    <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                    <template slot="title">
                        <p>是否确定注册该目标器？</p>
                        <a-checkbox>确认注册</a-checkbox>
                      </template>
                      <a>注册</a>
                    </a-popconfirm>
                  </span>
                  <span v-else>
                 <a-dropdown :trigger="['click']">
                     <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
                     <a-menu slot="overlay">
                       <a-menu-item key="0">
                          <a @click="() => more_Infor(true)"><span>详细信息</span></a>
                          <a-modal
                             title="XXX目标器详细信息"
                             centered
                             v-model="more_infor"
                             @ok="() => more_infor = false"
                              :width="500"
                              :visible="visible"
                            >
                               <a-table :columns="info_columns" :dataSource="info_data" :pagination="false"/>
                                  
                          </a-modal>
                       </a-menu-item>
                       <a-menu-item key="1">
                          <a @click="() => modify_Chap(true)"><span>CHAP认证</span></a>
                          <a-modal
                             title="XXX目标器CHAP认证"
                             centered
                             v-model="modify_chap"
                             @ok="() => modify_chap = false"
                              :width="500"
                              :visible="visible"
                            >
                               <a-form :form="form">
                                     <a-form-item>
                                       <div style="margin-left: -15px;">
                                         <div style="width:50px;float:left;margin-left: 55px;"><a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked/></div>
                                         <div style="width:300px;float:left"><p>启用CHAP验证</p></div>
                                       </div>
                                     </a-form-item>
                                     <a-form-item>
                                     <a-row>
                                       <a-col :sm="5" :xs="24">
                                           <span>用  户  名</span>
                                       </a-col>
                                       <a-col :sm="16" :xs="24">
                                         <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入用户名'}]}]" />
                                       </a-col>  
                                     </a-row>
                                     </a-form-item>
                                     <a-form-item>
                                     <a-row>
                                       <a-col :sm="5" :xs="24">
                                           <span>密     码</span>
                                       </a-col>
                                       <a-col :sm="16" :xs="24">
                                         <a-input type="password" placeholder="Password" v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入密码'}]}]" />
                                       </a-col>  
                                     </a-row>
                                     </a-form-item>
                               </a-form>
                          </a-modal>
                       </a-menu-item>
                       <a-menu-item key="2">
                         <a @click="() => setNet_Dns(true)"><a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                        <template slot="title">
                          <p>是否确定取消注册该目标器？</p>
                          <a-checkbox>确认取消注册</a-checkbox>
                        </template>
                        <span>取消注册</span>
                      </a-popconfirm></a>
                       </a-menu-item>
                     </a-menu>
                   </a-dropdown>
                   </span>
                 </span>
            </a-table>
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
    text: '已注册'
  },
  1: {
    status: 'default',
    text: '未注册'
  },
}
const statusOpen = {
  
  0: {
    status: 'success',
    text: '已开启'
  },
  1: {
    status: 'default',
    text: '未开启'
  },
  
  2: {
    text: '-'
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
      modify_chap: false,
      add_member: false,
      more_infor: false,
      selectedRowKeys: [],
      selectedRows: [],
      openKeys: [],
      selectedKeys: [],
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      info_columns: [
        {
          title: '属性',
          dataIndex: 'properties',
        },
        {
          title: '详情',
          dataIndex: 'info',
        }
      ],
      info_data: [
        {
          properties: '目标器地址',
          info: '192.168.1.205:3260'
        },
        {
          properties: '名称',
          info: 'iqn.2017-10.com.marstor:123'
        },
        {
          properties: '别名',
          info: 'Mars-iSCSI-T-1'
        },
        {
          properties: '目标组',
          info: '1'
        },
        {
          properties: '连接数',
          info: '1'
        },
        {
          properties: '认证类型',
          info: 'CHAP'
        },
        {
          properties: '认证用户',
          info: 'test'
        },
        {
          properties: '生产厂商',
          info: '-'
        },
        {
          properties: '产品型号',
          info: '-'
        },
        {
          properties: 'LUN信息',
          info: '-'
        },
        {
          properties: '设备名称',
          info: '-'
        },
      ],
      columns: [
        {
          title: '主机IP',
          dataIndex: 'ip',
        },
        {
          title: '端口',
          dataIndex: 'port',
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
          ip: '192.168.0.100',
          port: '3260'
        },
        {
          ip: '192.168.1.100',
          port: '3260'
        },
        {
          ip: '192.168.2.100',
          port: '3260'
        }
      ],
     incolumns: [
       {
         title: '目标器',
         dataIndex: 'target',
       },
       {
         title: '状态',
         dataIndex: 'status',
         scopedSlots: { customRender: 'status' }
       },
       {
         title: 'CHAP认证',
         dataIndex: 'chap',
         scopedSlots: { customRender: 'chap' }
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
         target: 'iqn.2017-10.com.marstor:123',
         status: '1',
         chap : '2'
       },
       {
         target: 'iqn.2017-10.com.marstor:123',
         status: '0',
         chap : '0'
       },
       {
         target: 'iqn.2017-10.com.marstor:123',
         status: '0',
         chap : '1'
       }
     ],
      modify_Chap(modify_chap){
        this.modify_chap = modify_chap},
      add_Member(add_member){
        this.add_member = add_member},
        more_Infor(more_infor){
          this.more_infor = more_infor
        },
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
    chapstatusFilter (type) {
      return statusOpen[type].text
    },
    chapstatusTypeFilter (type) {
      return statusOpen[type].status
    },
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
  methods: {
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
    },tableOption () {
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
