<template>
  <a-card :bordered="false">
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
      <a-form-item :wrapperCol="{span: 19, offset: 5}">
        <a-button  @click="prevStep"> <a-icon type="left" />上一步</a-button>
        <a-button type="primary" @click="nextStep">下一步<a-icon type="right" /></a-button>
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
    
      finish () {
        this.$router.push('/virtualtapelibrary/tapelibrary')
      },
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
