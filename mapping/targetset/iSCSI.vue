<template>
  <a-card :bordered="false">
    <div class="table-operator">
      <a-button type="primary" icon="plus" @click="() => add_Group(true)">新建目标器</a-button>
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
       title="新建目标器"
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
                   <span>目标器名称</span>
               </a-col>
               <a-col :sm="9" :xs="24">
                   <span>iqn.2019-12.com.marstor:</span>
               </a-col>
               <a-col :sm="7" :xs="24">
                 <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入目标器名称'}]}]" />
               </a-col>  
             </a-row>
             </a-form-item>
             <a-form-item>
               <a-row>
                 <a-col :sm="5" :xs="24">
                     <span>目标器别名</span>
                 </a-col>
                 <a-col :sm="16" :xs="24">
                   <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入目标器别名'}]}]" />
                 </a-col>  
               </a-row>
               </a-form-item>
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
               <a-form-item>
                 <div style="margin-bottom: -15px;">
                   <div style="width:50px;float:left;margin-left: 55px;"><a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked/></div>
                   <div style="width:300px;float:left"><p>绑定IP</p></div>
                 </div>
               </a-form-item>
               <a-form-item>
               <a-row>
                 <a-col :sm="5" :xs="24">
                     <span>选择IP地址</span>
                 </a-col>
                 <a-col :sm="16" :xs="24">
                <a-table :rowSelection="rowSelection" :columns="ip_columns" :dataSource="ip_data":pagination="false":size="small">
                   <a slot="name" slot-scope="text" href="javascript:;">{{text}}</a>
                 </a-table>
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
         <span slot="chap" slot-scope="text">
           <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
         </span>
         <span slot="ip" slot-scope="text">
           <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
         </span>
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
                     <p>是否确定删除该目标器？</p>
                     <a-checkbox>确认删除</a-checkbox>
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
    text: '已启用'
  },
  1: {
    status: 'default',
    text: '未启用'
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
      columns: [
        {
          title: '目标器iqn',
          dataIndex: 'iqn',
        },
        {
          title: '别名',
          dataIndex: 'name',
        },
        {
          title: 'CHAP',
          dataIndex: 'chap',
           scopedSlots: { customRender: 'chap' }
        },
        {
          title: 'IP绑定',
          dataIndex: 'ip',
           scopedSlots: { customRender: 'ip' }
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
          iqn: 'iqn.2019-11.com.marstor:1574909220779',
          name: 'Mars-iSCSI-T-1',
          chap: '1',
          ip: '0',
        },
        {
          iqn: 'iqn.2019-11.com.marstor:1574916203426',
          name: 'Mars-iSCSI-T-2',
          chap: '1',
          ip: '1',
        },
        {
          iqn: 'iqn.2019-11.com.marstor:1574910934731',
          name: 'Mars-iSCSI-T-3',
          chap: '0',
          ip: '1',
        }
      ],
     ip_columns: [
       {
         title: 'IP地址',
         dataIndex: 'ip',
       }
     ],
     ip_data: [
       {
         ip: '192.168.0.100'
       },
       {
         ip: '192.168.1.100'
       },
       {
         ip: '192.168.2.100'
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
