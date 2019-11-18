<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="磁盘名称">
              <a-input v-model="queryParam.id" placeholder=""/>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="磁盘状态">
              <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                <a-select-option value="0">在线</a-select-option>
                <a-select-option value="1">故障</a-select-option>
                <a-select-option value="2">已弹出</a-select-option>
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
        <a-button >所有硬盘</a-button>
        <a-button >SATA硬盘</a-button>
        <a-button >SAS硬盘</a-button>
        <a-button >NL-SAS硬盘</a-button>
        <a-button >SSD硬盘</a-button>
        <a-button >NVME</a-button><!-- 
        <a-button @click="All">所有硬盘</a-button>
        <a-button @click="SATA">SATA硬盘</a-button>
        <a-button @click="SAS">SAS硬盘</a-button>
        <a-button @click="NL-SAS">NL-SAS硬盘</a-button>
        <a-button @click="SSD">SSD硬盘</a-button>
        <a-button @click="NVME">NVME</a-button> -->
        <a-dropdown v-if="hasSelected">
          <a-menu slot="overlay">
            <a-menu-item key="1"><a-icon type="logout" />弹出硬盘</a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px">
            批量操作 <a-icon type="down" />
          </a-button>
        </a-dropdown>
    </div>
    <div>
    <a-alert message="↑上边的磁盘按钮应按照实际拥有的磁盘类型清单显示,↓下方的复选框应只有未分配的可选;该提示内容应与选择的硬盘类型有关" banner closable />
    </div>
   <template>
     <a-table :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}" :columns="columns" :dataSource="data">
       <span slot="status" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
     </a-table>
   </template>
   
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
  }
}
export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis
    // CreateForm,
    // StepByStepModal
  },
  data () {
    return {
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
       columns: [
        {
          title: '状态',
          dataIndex: 'status',
          scopedSlots: { customRender: 'status' }
        },
        {
          title: '磁盘名称',
          dataIndex: 'disk_name'
        },
        {
          title: '磁盘位置',
          dataIndex: 'disk_loc',
          scopedSlots: { customRender: 'disk_loc' }
        },{
          title: '磁盘类型',
          dataIndex: 'disk_inte',
          scopedSlots: { customRender: 'disk_inte' }
        },{
          title: '磁盘容量',
          dataIndex: 'disk_vol',
          scopedSlots: { customRender: 'disk_vol' }
        },{
          title: '使用状态',
          dataIndex: 'disk_use',
          scopedSlots: { customRender: 'disk_use' }
        }
      ],
      data:[
        {
          key: '1',
          status:'0',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0tAD00000500D60000d0',
          disk_loc: 'Host-1-1',
          disk_use:'SYSVOL（数据卷）'
        },
        {
          key: '2',
          status:'0',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t50F000000C621972d0',
          disk_loc: 'Host-1-2',
          disk_use:'SYSVOL（数据卷）'
        },
        {
          key: '3',
          status:'0',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t500C500129730ABd0',
          disk_loc: 'JBod-1-1',
          disk_use:'SYSVOL（热备盘）'
        },
        {
          key: '4',
          status:'0',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t5000CCA224D3FF2Ed0',
          disk_loc: 'JBod-1-2',
          disk_use:'TEST（数据卷）'
        },
        {
          key: '5',
          status:'1',
          disk_inte:'SATA',
          disk_vol:'6TB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-1',
          disk_use:'SYSVOL（数据卷）'
        },
        {
          key: '6',
          status:'0',
          disk_inte:'SAS',
          disk_vol:'1.8TB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-1-9',
          disk_use:'未使用'
        },
        {
          key: '7',
          status:'0',
          disk_inte:'NL-SAS',
          disk_vol:'6TB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-6',
          disk_use:'未使用'
        },
        {
          key: '8',
          status:'0',
          disk_inte:'SSD',
          disk_vol:'240GB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-11',
          disk_use:'SYSVOL（读缓存）'
        },
        {
          key: '9',
          status:'0',
          disk_inte:'NVME',
          disk_vol:'480GB',
          disk_name: 'c0t5001CDB224D3FC2Ed0',
          disk_loc: 'JBod-2-12',
          disk_use:'SYSVOL（写缓存）'
        }
      ],
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        console.log('loadData.parameter', parameter)
        return getServiceList(Object.assign(parameter, this.queryParam))
          .then(res => {
            return res.result
          })
      },
      selectedRowKeys: [],
      selectedRows: [],

      // custom table alert & rowSelection
      options: {
        alert: { show: false, clear: () => { this.selectedRowKeys = [] } },
        rowSelection: {
          selectedRowKeys: this.selectedRowKeys,
          onChange: this.onSelectChange
        }
      },
      optionAlertShow: false
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
        const { selectedRowKeys } = this.selectedRowKeys;
        return {
          onChange: (selectedRowKeys, selectedRows) => {
            console.log(`selectedRowKeys: ${selectedRowKeys.length}`, 'selectedRows: ', selectedRows);
          },
          getCheckboxProps: record => ({
            props: {
              disabled: record.id > 5, // Column configuration not to be checked  
            }
          }),
        }
      },
      hasSelected() {
        return this.selectedRowKeys.length > 0
      }
    },
  methods: {
    start () {
          this.loading = true;
          // ajax request after empty completing
          setTimeout(() => {
            this.loading = false;
            this.selectedRowKeys = [];
          }, 1000);
        },
        onSelectChange (selectedRowKeys) {
          console.log('selectedRowKeys changed: ', selectedRowKeys);
          this.selectedRowKeys = selectedRowKeys
          
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
                    disabled: record.disk_use === '未使用', // Column configuration not to be checked
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
      remove (key) {
      const newData = this.data.filter(item => item.key !== key)
      this.data = newData
    },
  }
}
</script>
