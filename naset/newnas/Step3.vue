<template>
  <a-card :bordered="false">
    <a-form :form="form">
      <a-form-item>
        <a-row :gutter="20">
          <a-col :sm="5" :xs="24" />
          <a-col :sm="9" :xs="24">
            <a-tree-select
                :treeData="permissions"
                :value="value"
                @change="onChange"
                treeCheckable
                :showCheckedStrategy="SHOW_PARENT"
                searchPlaceholder="Please select"
              />
          </a-col>
          <a-col :sm="6" :xs="24">
            <a-button type="primary" icon="plus">添加</a-button>
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
        label: customLabel, // for displayed item
        value: item.title, // for title and filter matching
      };
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
