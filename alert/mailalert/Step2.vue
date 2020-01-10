<template>
  <a-card :bordered="false">
    <div class="table-operator">
      <a-button type="primary" icon="plus" @click="() => Modify_Strategy(true)">新建策略</a-button>
      <a-modal
          title="设置策略"
          centered
          v-model="modify_strategy"
          @ok="() => modify_strategy = false"
          width = "600"
        >
          <template>
            <a-form style="margin: 40px auto 0;">
              <a-alert
                    message="在勾选复选框之后才能激活该条告警设置,当前全选按钮没有做关联"
                    banner
                    closable
                  />
                <a-form-item label="火星舱设备信息" 
                             :labelCol="labelCol"
                             :wrapperCol="wrapperCol">
                 <div style="float:left;">
                       <a-checkbox   >
                         全选
                       </a-checkbox>
                 </div>
                 </br>
                 <div style="text-align:center;borderBottom:1px solid #E9E9E9">
                  <a-row>
                    <a-col :sm="12" :xs="24">
                      <div style="float:left;">
                        <a-checkbox>CPU使用率告警阀值</a-checkbox>
                      </div>
                    </a-col>
                    <a-col :sm="12" :xs="24"><a-input-number
                      :defaultValue="100"
                      :min="0"
                      :max="100"
                      :formatter="value => `${value}%`"
                      :parser="value => value.replace('%', '')"
                     
                    /></a-col>
                  </a-row>  
                  <a-row>
                    <a-col :sm="12" :xs="24">
                      <div style="float:left;">
                        <a-checkbox>CPU温度</a-checkbox>
                      </div>
                    </a-col>
                    <a-col :sm="12" :xs="24"><a-input-number
                      :defaultValue="100"
                      :min="0"
                      :max="200"
                      :formatter="value => `${value}℃`"
                      :parser="value => value.replace('℃', '')"
                     
                    /></a-col>
                  </a-row>  
                  <a-row>
                    <a-col :sm="12" :xs="24">
                      <div style="float:left;">
                        <a-checkbox>硬盘故障</a-checkbox>
                      </div>
                    </a-col>
                  </a-row>  
                  <a-row>
                    <a-col :sm="12" :xs="24">
                      <div style="float:left;">
                        <a-checkbox>容量告警阀值</a-checkbox>
                      </div>
                    </a-col>
                    <a-col :sm="12" :xs="24"><a-input-number
                      :defaultValue="100"
                      :min="0"
                      :max="100"
                      :formatter="value => `${value}%`"
                      :parser="value => value.replace('%', '')"
                     
                    /></a-col>
                  </a-row> 
                  <a-row>
                    <a-col :sm="12" :xs="24">
                      <div style="float:left;">
                        <a-checkbox>硬盘温度</a-checkbox>
                      </div>
                    </a-col>
                    <a-col :sm="12" :xs="24"><a-input-number
                      :defaultValue="100"
                      :min="0"
                      :max="200"
                      :formatter="value => `${value}℃`"
                      :parser="value => value.replace('℃', '')"
                     
                    /></a-col>
                  </a-row> 
                  <a-row>
                    <a-col :sm="12" :xs="24">
                      <div style="float:left;">
                        <a-checkbox>网络流量</a-checkbox>
                      </div>
                    </a-col>
                    <a-col :sm="12" :xs="24"><a-input-number
                      :defaultValue="1000"
                      :min="0"
                      :max="8000"
                      :formatter="value => `${value}MB`"
                      :parser="value => value.replace('MB', '')"
                     
                    /></a-col>
                  </a-row> 
                  </div>
                </a-form-item>
                <a-form-item label="NAS功能告警" 
                             :labelCol="labelCol"
                             :wrapperCol="wrapperCol">
                  <div style="float:left;">
                        <a-checkbox   >
                          全选
                        </a-checkbox>
                  </div>
                  </br>
                  <div style="text-align:center;borderBottom:1px solid #E9E9E9">
                   <a-row>
                     <a-col :sm="12" :xs="24">
                       <div style="float:left;">
                         <a-checkbox>空间使用率阀值</a-checkbox>
                       </div>
                     </a-col>
                     <a-col :sm="12" :xs="24"><a-input-number
                       :defaultValue="100"
                       :min="0"
                       :max="100"
                       :formatter="value => `${value}%`"
                       :parser="value => value.replace('%', '')"
                      
                     /></a-col>
                   </a-row>  
                  </div>           
                </a-form-item>
                <a-form-item label="VTL功能告警"
                             :labelCol="labelCol"
                             :wrapperCol="wrapperCol">
                  <div style="float:left;">
                        <a-checkbox   >
                          全选
                        </a-checkbox>
                  </div>
                  </br>
                  <div style="text-align:center;borderBottom:1px solid #E9E9E9">
                   <a-row>
                     <a-col :sm="12" :xs="24">
                       <div style="float:left;">
                         <a-checkbox>空间使用率阀值</a-checkbox>
                       </div>
                     </a-col>
                     <a-col :sm="12" :xs="24"><a-input-number
                       :defaultValue="100"
                       :min="0"
                       :max="100"
                       :formatter="value => `${value}%`"
                       :parser="value => value.replace('%', '')"
                      
                     /></a-col>  
                   </a-row>
                  </div>           
                </a-form-item>
                <a-form-item label="CDP功能告警"
                             :labelCol="labelCol"
                             :wrapperCol="wrapperCol">
                  <div style="float:left;">
                        <a-checkbox   >
                          全选
                        </a-checkbox>
                  </div>
                  </br>
                  <div style="text-align:center;borderBottom:1px solid #E9E9E9">
                   <a-row>
                     <a-col :sm="12" :xs="24">
                       <div style="float:left;">
                         <a-checkbox>使用率阀值</a-checkbox>
                       </div>
                     </a-col>
                     <a-col :sm="12" :xs="24"><a-input-number
                       :defaultValue="100"
                       :min="0"
                       :max="100"
                       :formatter="value => `${value}%`"
                       :parser="value => value.replace('%', '')"
                      
                     /></a-col>
                   </a-row>  
                   <a-row>
                     <a-col :sm="12" :xs="24">
                       <div style="float:left;">
                         <a-checkbox>故障告警</a-checkbox>
                       </div>
                     </a-col>
                   </a-row>  
                  </div>           
                </a-form-item>
                <a-form-item label="CDM备份功能告警"
                             :labelCol="labelCol"
                             :wrapperCol="wrapperCol">
                  <div style="float:left;">
                        <a-checkbox   >
                          全选
                        </a-checkbox>
                  </div>
                  </br>
                  <div style="text-align:center;">
                   <a-row>
                     <a-col :sm="12" :xs="24">
                       <div style="float:left;">
                         <a-checkbox>使用率阀值</a-checkbox>
                       </div>
                     </a-col>
                     <a-col :sm="12" :xs="24"><a-input-number
                       :defaultValue="100"
                       :min="0"
                       :max="100"
                       :formatter="value => `${value}%`"
                       :parser="value => value.replace('%', '')"
                      
                     /></a-col>
                   </a-row>  
                   <a-row>
                     <a-col :sm="12" :xs="24">
                       <div style="float:left;">
                         <a-checkbox>任务异常告警</a-checkbox>
                       </div>
                     </a-col>
                   </a-row>  
                  </div>           
                </a-form-item>
                <a-form-item label="日志功能告警"
                             :labelCol="labelCol"
                             :wrapperCol="wrapperCol">
                  <div style="float:left;">
                        <a-checkbox   >
                          全选
                        </a-checkbox>
                  </div>
                  </br>
                  <div style="text-align:center;borderBottom:1px solid #E9E9E9">
                   <a-row>
                     <a-col :sm="12" :xs="24">
                       <div style="float:left;">
                         <a-checkbox>空间使用率阀值</a-checkbox>
                       </div>
                     </a-col>
                     <a-col :sm="12" :xs="24"><a-input-number
                       :defaultValue="100"
                       :min="0"
                       :max="100"
                       :formatter="value => `${value}%`"
                       :parser="value => value.replace('%', '')"
                      
                     /></a-col>  
                   </a-row>
                  </div>           
                </a-form-item>
            </a-form>
          </template>
        </a-modal>
    </div>

    <a-table
      ref="table"
      size="default"
      :columns="columns"
      :dataSource="data"
    >
      <span slot="status" slot-scope="text">
        <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
      </span>
      <span slot="strategy_pro" slot-scope="strategy_pro">
      <a-tag v-for="tag in strategy_pro" color="blue" :key="tag">{{tag}}</a-tag>
      </span>
      <span slot="action" slot-scope="text, record">
        <span><a @click="() => Modify_Strategy(true)">编辑</a></span>
         <a-divider type="vertical" />
          <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
            <template slot="title">
              <p>是否确认删除该策略？</p>
			  <a-checkbox>确认修改</a-checkbox>
            </template>
            <span><a>删除</a></span>
          </a-popconfirm>
        </span>
    </a-table>
    <div style="text-align:center;">
        <a-button  @click="prevStep"> <a-icon type="left" />上一步</a-button>
        <a-button type="primary" @click="nextStep">下一步<a-icon type="right" /></a-button>
    </div>
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
