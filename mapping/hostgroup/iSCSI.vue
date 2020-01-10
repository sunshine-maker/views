<template>
  <a-card :bordered="false">
    <div class="table-operator">
      <a-button type="primary" icon="plus" @click="() => add_Group(true)">新建主机组</a-button>
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
    <a-modal
       title="新建主机组"
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
                   <span>主机组名称</span>
               </a-col>
               <a-col :sm="2" :xs="24">
                     iscsi_
               </a-col> 
               <a-col :sm="10" :xs="24">
                     <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入主机组名称'}]}]" />
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
     <a-table :columns="columns" :dataSource="data" class="components-table-demo-nested">
       <span slot="status" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
       <span slot="action" slot-scope="text, record">
         <a-dropdown :trigger="['click']">
             <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
             <a-menu slot="overlay">
               <a-menu-item key="0">
                  <a @click="() => add_Member(true)"><span>添加成员</span></a>
               </a-menu-item>
               <a-menu-item key="1">
                 <a @click="() => setNet_Dns(true)"><a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                <template slot="title">
                  <p>是否确定删除该主机组？</p>
                  <a-checkbox>确认删除</a-checkbox>
                </template>
                <span>删除</span>
              </a-popconfirm></a>
               </a-menu-item>
             </a-menu>
           </a-dropdown>
         <a-modal
             title="添加XXX主机组成员"
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
                    label="成员iqn"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <a-select
                         v-decorator="[
                           'select',
                           {rules: [{ required: true, message: '请选择/添加成员iqn' }]}
                         ]"
                         placeholder="请选择/添加成员iqn"
                       >
                         <a-select-option value="iqn">
                           iqn
                         </a-select-option>
                       </a-select>
                    </a-form-item>
                      <a-form-item>
                        <div style="margin-bottom: -15px;">
                          <div style="width:50px;float:left;margin-left: 55px;"><a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked/></div>
                          <div style="width:300px;float:left"><p>启用CHAP验证</p></div>
                        </div>
                      </a-form-item>
                      <a-form-item
                        label="用户名"
                        :labelCol="labelCol"
                        :wrapperCol="wrapperCol"
                      >
                      <a-input/>
                      </a-form-item>
                      <a-form-item
                        label="密  码"
                        :labelCol="labelCol"
                        :wrapperCol="wrapperCol"
                      >
                      <a-input type="password" placeholder="Password" />
                      </a-form-item>
                </a-form>
              </a-spin>
           </a-modal>
       </span>
       
       <span slot="expandedRowRender" slot-scope="record" style="margin: 0" >
         <a-table :columns="incolumns" :dataSource="indata" :pagination="false" :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}">
          <span slot="status" slot-scope="text">
            <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
          </span>
          <span slot="action" slot-scope="text, record">
            <a-dropdown :trigger="['click']">
                <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
                <a-menu slot="overlay">
                  <a-menu-item key="0">
                     <a @click="() => add_Member(true)"><span>修改</span></a>
                  </a-menu-item>
                  <a-menu-item key="1">
                    <a @click="() => setNet_Dns(true)"><a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                   <template slot="title">
                     <p>是否确定删除该iqn？</p>
                     <p>删除该iqn将导致正在使用的改链接的设备丢失信号！</p>
                     <a-checkbox>确认修改</a-checkbox>
                   </template>
                   <span>删除</span>
                 </a-popconfirm></a>
                  </a-menu-item>
                </a-menu>
              </a-dropdown>
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
           title: '主机组名称',
           dataIndex: 'groupname',
           scopedSlots: { customRender: 'groupname' }
         },
         {
           title: '成员数量',
           dataIndex: 'member'
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
           groupname: 'iscsi_001',
           member: '1'
         },
         {
           key: '1',
           groupname: 'iscsi_002',
           member: '2'
         },
         {
           key: '2',
           groupname: 'iscsi_003',
           member: '3',
         }
       ],
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
