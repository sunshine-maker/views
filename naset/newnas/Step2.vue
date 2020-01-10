<template>
  <a-card :bordered="false">
  <a-form :form="form">
    <a-divider>CIFS 设置</a-divider>
    <a-form-item>
      <a-row :gutter="20">
        <a-col :sm="10" :xs="24">
         <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
         <span>开启 CIFS 共享</span>
        </a-col>
      </a-row >
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24">
          <span>共享名称：</span>
        </a-col>
        <a-col :sm="13" :xs="24">
          <a-input />
        </a-col>  
      </a-row>
    </a-form-item>
    <a-divider>NFS 设置</a-divider>
    <a-form-item>
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24">
         <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
         <span>开启 NFS 共享</span>
        </a-col>
      </a-row >
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24">
         <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
         <span>开启匿名访问</span>
        </a-col>
        <a-col :sm="7" :xs="24">
         <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
         <span>开启匿名读写</span>
        </a-col>
      </a-row >
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24">
          <span>读写权限：</span>
        </a-col>
        <a-col :sm="10" :xs="24">
          <a-input />
        </a-col>  
        <a-col :sm="3" :xs="24">
          <a-button type="primary" icon="plus">添加</a-button>
        </a-col>  
      </a-row>
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24"/>
        <a-col :sm="13" :xs="24">
          <a-list size="small" :dataSource="rwdata">
            <a-list-item slot="renderItem" slot-scope="item, index">
              <div style="float: left;">
                <div style="float: left;width:150px">
                 {{item}}
                </div>
                <div style="float: right; width:30px;">
                  <a><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                    <template slot="title">
                      <p>是否确定<b style="color:red">删除</b>该目标？</p>
                      <a-checkbox>确认删除</a-checkbox>
                    </template>
                    <a>删除</a>
                  </a-popconfirm></a>
                </div>
              </div>
            </a-list-item>
          </a-list>
        </a-col>  
      </a-row>
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24">
          <span>只读权限：</span>
        </a-col>
        <a-col :sm="10" :xs="24">
          <a-input />
        </a-col>  
        <a-col :sm="3" :xs="24">
          <a-button type="primary" icon="plus">添加</a-button>
        </a-col>  
      </a-row>
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24"/>
        <a-col :sm="13" :xs="24">
          <a-list size="small" :dataSource="rwdata">
            <a-list-item slot="renderItem" slot-scope="item, index">
              <div style="float: left;">
                <div style="float: left;width:150px">
                 {{item}}
                </div>
                <div style="float: right; width:30px;">
                  <a><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                    <template slot="title">
                      <p>是否确定<b style="color:red">删除</b>该目标？</p>
                      <a-checkbox>确认删除</a-checkbox>
                    </template>
                    <a>删除</a>
                  </a-popconfirm></a>
                </div>
              </div>
            </a-list-item>
          </a-list>
        </a-col>  
      </a-row>
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24">
          <span>ROOT权限：</span>
        </a-col>
        <a-col :sm="10" :xs="24">
          <a-input />
        </a-col>  
        <a-col :sm="3" :xs="24">
          <a-button type="primary" icon="plus">添加</a-button>
        </a-col>  
      </a-row>
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24"/>
        <a-col :sm="13" :xs="24">
          <a-list size="small" :dataSource="rwdata">
            <a-list-item slot="renderItem" slot-scope="item, index">
              <div style="float: left;">
                <div style="float: left;width:150px">
                 {{item}}
                </div>
                <div style="float: right; width:30px;">
                  <a><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                    <template slot="title">
                      <p>是否确定<b style="color:red">删除</b>该目标？</p>
                      <a-checkbox>确认删除</a-checkbox>
                    </template>
                    <a>删除</a>
                  </a-popconfirm></a>
                </div>
              </div>
            </a-list-item>
          </a-list>
        </a-col>  
      </a-row>
      <a-row :gutter="20">
        <a-col :sm="7" :xs="24">
         <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
         <span>开启高级设置</span>
        </a-col>
        <a-col :sm="13" :xs="24">
         <a-textarea placeholder="Basic usage" :rows="4" />
        </a-col>
      </a-row >
    </a-form-item>
    <a-form-item :wrapperCol="{span: 19, offset: 5}">
      <a-button  @click="prevStep" ><a-icon type="left" />上一步</a-button>
      <a-button type="primary"@click="nextStep">下一步<a-icon type="right" /></a-button>
      <a-button style="margin-left: 8px"  type="primary" @click="finish">提交</a-button>
    </a-form-item>
  </a-form>
     
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
    status: 'success',
    text: '使用中'
  },
  1: {
    status: 'default',
    text: '未使用'
  },
}
export default {
  name: 'Step2',
  components: {
    STable,
    Ellipsis
    // CreateForm,
    // StepByStepModal
  },
  data () {
    return {
      labelCol: { lg: { span: 5 }, sm: { span: 5 } },
      wrapperCol: { lg: { span: 19 }, sm: { span: 19 } },
      form: this.$form.createForm(this),
      loading: false,
      timer: 0,
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      modify_strategy: false,
      columns: [
         {
           title: '使用状态',
           dataIndex: 'status',
           scopedSlots: { customRender: 'status' }
         },
         {
           title: '策略名称',
           dataIndex: 'strategy_name'
         },
         {
           title: '策略简况',
           dataIndex: 'strategy_pro',
           scopedSlots: { customRender: 'strategy_pro' }
         },
         {
           title: '策略使用数',
           dataIndex: 'strategy_times',
           scopedSlots: { customRender: 'strategy_times' }
         },
         {
           title: '操作',
           dataIndex: 'action',
           width: '200px',
           scopedSlots: { customRender: 'action' }
         }
       ],
       data: [
         {
           key: '0',
           status: '0',
           strategy_name: '策略1',
           strategy_pro: ['设备','CDP','VTL'],
           strategy_times: '1'
         },
         {
           key: '1',
           status: '0',
           strategy_name: '策略2',
           strategy_pro: ['CDM','统一备份'],
           strategy_times: '2'
         },
         {
           key: '2',
           status: '1',
           strategy_name: '策略3',
           strategy_pro: ['NAS'],
           strategy_times: '0'
         }
       ],
      
      selectedRowKeys: [],
      selectedRows: [],

      // custom table alert & rowSelection
      
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
  
  methods: {
     onToggle() {
            this.disabled = !this.disabled;
          },
    finish () {
      this.$router.push('/virtualtapelibrary/tapelibrary')
    },
   nextStep () {
         this.$emit('nextStep')
   },
   prevStep () {
     this.$emit('prevStep')
   },
   Modify_Strategy(modify_strategy) {
           this.modify_strategy = modify_strategy;
         },
    handleEdit (record) {
      console.log(record)
      this.$refs.modal.edit(record)
    },
  }
}
</script>
