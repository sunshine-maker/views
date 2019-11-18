<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="邮箱名称">
              <a-input v-model="queryParam.id" placeholder=""/>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="邮箱状态">
              <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                <a-select-option value="0">启用</a-select-option>
                <a-select-option value="1">禁用</a-select-option>
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
      <a-button type="primary" icon="plus" @click="() => Create_Mail(true)">添加邮箱</a-button>
      
      <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1">
            <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
              <template slot="title">
                <p>是否确认删除该邮箱？</p>
              </template>
              <a-button>删除邮箱</a-button>
            </a-popconfirm>
           </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作 <a-icon type="down" />
        </a-button>
      </a-dropdown>
    </div>
	<div>
    
      <a-form :form="form" >
        <a-form-item>
	<a-table
	      :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
	      :columns="columns"
	      :dataSource="data"
	    >
      <span slot="status" slot-scope="text">
        <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
      </span>
       <span slot="description" slot-scope="description">
       <a-tag v-for="tag in description" color="blue" :key="tag">{{tag}}</a-tag>
       </span>
       <span slot="action" slot-scope="text, record">
         <span v-if="record.status === '0'">
           <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
             <template slot="title">
               <p>是否确认启用该邮箱？</p>
             </template>
             <span><a>启用</a></span>
           </a-popconfirm>
         </span>
         <span v-else>
           <span><a @click="() => Modify_Mail(true)">编辑</a></span>
           <a-divider type="vertical" />
           <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
             <template slot="title">
               <p>是否确认禁用该邮箱？</p>
             </template>
             <span><a>禁用</a></span>
           </a-popconfirm>
           <a-divider type="vertical" />
           <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
             <template slot="title">
               <p>是否确认删除该邮箱？</p>
             </template>
             <span><a>删除</a></span>
           </a-popconfirm>
         </span>
       </span>
  </a-table>

      </a-form-item>
      <a-form-item >
        <div style="text-align:center;">
        <a-button  @click="prevStep">上一步</a-button>
        <a-button type="primary" style="margin-left: 8px" @click="nextStep">下一步</a-button>
       </div>
      </a-form-item>
    </a-form>
  <a-modal
      title="添加邮箱"
      centered
      v-model="create_mail"
      @ok="() => create_mail = false"
      :width="500"
    >
    <div>
    <a-alert
          message="显示格式存在问题"
          banner
          closable
        />
        </div>
           <a-form :form="form" style="max-width: 400px; margin: 40px auto 0;">
          <a-form-item label="邮箱地址" 
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol">
            <a-input
              v-decorator="[
                'email',
                {
                  rules: [
                    {
                      type: 'email',
                      message: 'The input is not valid E-mail!',
                    },
                    {
                      required: true,
                      message: 'Please input your E-mail!',
                    },
                  ],
                },
              ]"
            />
          </a-form-item>
          <a-form-item  label="邮箱状态" 
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol">
              <a-select defaultValue="启用">
                    <a-select-option value="enabled">启用</a-select-option>
                    <a-select-option value="disabled">禁用</a-select-option>
                  </a-select>
          </a-form-item>
          <a-form-item label="告警范围" 
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol">
            <a-select
                mode="multiple"
                :size="size"
                placeholder="Please select"
                  :defaultValue="['策略1']"
                  @change="handleChange"
                  @popupScroll="popupScroll"
                >
                  <a-select-option value="1">策略1</a-select-option>
                  <a-select-option value="2">策略2</a-select-option>
                  <a-select-option value="3">策略3</a-select-option>
                
              </a-select>
          
          </a-form-item>
        </a-form>
    </a-modal>
    <a-modal title="修改邮箱" v-model="modify_mail" @ok="handleOk">
      <div>
      <a-alert
            message="显示格式存在问题"
            banner
            closable
          />
          </div>
             <a-form :form="form" style="max-width: 400px; margin: 40px auto 0;">
            <a-form-item label="邮箱地址" 
                       :labelCol="labelCol"
                       :wrapperCol="wrapperCol">
              <a-input
                v-decorator="[
                  'email',
                  {
                    rules: [
                      {
                        type: 'email',
                        message: 'The input is not valid E-mail!',
                      },
                      {
                        required: true,
                        message: 'Please input your E-mail!',
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
            <a-form-item  label="邮箱状态" 
                       :labelCol="labelCol"
                       :wrapperCol="wrapperCol">
                <a-select defaultValue="启用">
                      <a-select-option value="enabled">启用</a-select-option>
                      <a-select-option value="disabled">禁用</a-select-option>
                    </a-select>
            </a-form-item>
            <a-form-item label="告警范围" 
                       :labelCol="labelCol"
                       :wrapperCol="wrapperCol">
              <a-select
                  mode="multiple"
                  :size="size"
                  placeholder="Please select"
                  :defaultValue="['策略1']"
                  @change="handleChange"
                  @popupScroll="popupScroll"
                >
                  <a-select-option value="1">策略1</a-select-option>
                  <a-select-option value="2">策略2</a-select-option>
                  <a-select-option value="3">策略3</a-select-option>
                  
                </a-select>
            
            </a-form-item>
          </a-form>
    </a-modal>
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
    status: 'default',
    text: '禁用'
  },
  1: {
    status: 'success',
    text: '启用'
  }
}
export default {
  name: 'Step3',
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
      create_mail: false,
      modify_mail: false,
      // 表头
      columns: [
        {
          title: '邮箱',
          dataIndex: 'mail',
          scopedSlots: { customRender: 'serial' }
        },
        {
          title: '邮箱状态',
          dataIndex: 'status',
          scopedSlots: { customRender: 'status' }
        },
        {
          title: '告警范围',
          dataIndex: 'description',
          scopedSlots: { customRender: 'description' }
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '200px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      data:[
        {
          key: '0',
          mail: 'ma.lianyu@marstor.com',
          status: '0',
          description: ['策略1'],
        },
        {
          key: '1',
          mail: 'li.wenchao@marstor.com',
          status: '1',
          description: ['策略2','策略3'],
        }
      ],
      selectedRowKeys: [],
      selectedRows: [],

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
    hasSelected() {
      return this.selectedRowKeys.length > 0
    }
  },
  methods: {
    nextStep () {
      const { form: { validateFields } } = this
      // 先校验，通过表单校验后，才进入下一步
      validateFields((err, values) => {
        if (!err) {
          this.$emit('nextStep')
        }
      })
    },
    prevStep () {
      this.$emit('prevStep')
    },
    Create_Mail() {
            this.create_mail = true;
          },
    Modify_Mail() {
            this.modify_mail = true;
          },
    beforeDestroy () {
      clearTimeout(this.timer)
    },
    onSelectChange(selectedRowKeys) {
      console.log('selectedRowKeys changed: ', selectedRowKeys);
      this.selectedRowKeys = selectedRowKeys;
    },
  },
};
</script>
