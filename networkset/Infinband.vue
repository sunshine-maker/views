<template>
  
  <a-card :bordered="false">
    <div>
    <a-alert message="********" banner closable />
    </div>
   <template>
    <a-table :columns="columns" :dataSource="data" class="components-table-demo-nested">
      <span slot="status" slot-scope="text">
        <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
      </span>
        <a slot="operation" slot-scope="text" @click="() => add_Partation(true)">创建分区</a>
        <a-table
          slot="expandedRowRender"
          slot-scope="text"
          :columns="innercolumns"
          :dataSource="innerdata"
          :pagination="false"
        >
          <span slot="operation" slot-scope="text" class="table-operation">
            <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
              <template slot="title">
                <p>是否确定删除该分区？</p>
              </template>
              <a>删除</a>
            </a-popconfirm>
          </span>
        </a-table>
      </a-table>
   </template>
    <a-modal
        title="添加XXXX分区"
        centered
        v-model="add_partation"
        @ok="() => add_partation = false"
        :width="500"
        :visible="visible"
        :confirmLoading="confirmLoading"
      >
        <template>
          <a-spin :spinning="confirmLoading">
                <a-form :form="form">
                  <a-form-item
                    label="PKey"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                    <div style="float:left">
                      <a-input 
                      :value="value"
                      @change="onChange"
                      @blur="onBlur"
                      :maxLength="25"
                      style="width: 200px"
                      v-decorator="['desc', {rules: [{required: true, min: 5, message: '请输入分区的PKey'}]}]" 
                      />
                    </div>
                  </a-form-item>
                </a-form>
              </a-spin>
        </template>
   </a-modal>
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
    text: '在线'
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
      add_partation: false,
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      columns: [
         {
           title: '网口状态',
           dataIndex: 'status',
           scopedSlots: { customRender: 'status' }
         },
         {
           title: '网口名称',
           dataIndex: 'network_name'
         },
         {
           title: 'Pkeys',
           dataIndex: 'network_Pkeys',
           scopedSlots: { customRender: 'network_Pkeys' }
         },
         {
           title: '操作',
           dataIndex: 'operation',
           width: '100px',
           scopedSlots: { customRender: 'operation' }
         }
       ],
       data: [
         {
           key: '0',
           status: '0',
           network_name: 'ibp0',
           network_Pkeys: 'FFF'
         }
       ],
      innercolumns: [
         {
           title: '分区名称',
           dataIndex: 'name',
           scopedSlots: { customRender: 'name' }
         },
         {
           title: 'Pkeys',
           dataIndex: 'network_Pkeys',
           scopedSlots: { customRender: 'network_Pkeys' }
         },
         {
           title: '操作',
           dataIndex: 'operation',
           width: '100px',
           scopedSlots: { customRender: 'operation' }
         }
       ],
       innerdata: [
         {
           key: '0',
           name: 'ibp0.p0',
           network_Pkeys: 'FFF'
         },
         {
           key: '1',
           name: 'ibp0.p1',
           network_Pkeys: 'FFF'
         }
       ],
      add_Partation(add_partation){
        this.add_partation = add_partation},
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
    },
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    }
  }
}
</script>
