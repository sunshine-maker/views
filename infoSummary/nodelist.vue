<template>
  <div class="page-header-index-wide">
    <a-card :bordered="false" :bodyStyle="{ padding: '16px 0', height: '100%' }" :style="{ height: '100%' }">
        <a-button type="primary" icon="upload" @click="() => Add_Group(true)">添加分组</a-button>
        <a-modal
             title="新加分组"
             centered
             v-model="add_group"
             @ok="() => add_group = false"
             :width="500"
             :visible="visible"
           >
                   <a-form :form="form">
                    
                     <a-form-item
                       label="分组名称"
                       :labelCol="labelCol"
                       :wrapperCol="wrapperCol"
                     >
                       <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的掩码地址'}]}]" />
                     </a-form-item>
                   </a-form>
           </a-modal>
        <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0" >
          <a-menu slot="overlay">
            <a-menu-item key="1">
              <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                <template slot="title">
                  <p>是否确定批量删除所有已选节点？</p>
                </template>
                <a-icon type="delete"/> <a>批量删除</a>
              </a-popconfirm>
            </a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px">
            批量操作 <a-icon type="down" />
          </a-button>
        </a-dropdown>
      <div>
         <a-table :columns="columns" :dataSource="data" :pagination="false" class="components-table-demo-nested" :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}">
           <span slot="status" slot-scope="text">
             <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
           </span>
           <span slot="action" slot-scope="text, record">    
             <a-dropdown :trigger="['click']">
                 <a class="ant-dropdown-link" > 配置<a-icon type="down" /> </a>
                 <a-menu slot="overlay">
                   <a-menu-item key="0">
                      <span><a @click="() => Add_Group(true)">修改组名</a></span>
                   </a-menu-item>
                   <a-menu-item key="1">
                     <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                       <template slot="title">
                         <p>是否确定删除该分组及下属所有节点？</p>
                       </template>
                       <span><a>删除分组</a></span>
                     </a-popconfirm>
                   </a-menu-item>
                   <a-menu-item key="3">
                      <span><a @click="() => Add_Device(true)">添加设备</a></span>
                   </a-menu-item>
                 </a-menu>
               </a-dropdown>
            </span>
            <span slot="expandedRowRender" slot-scope="record" style="margin: 0">
              <span v-if="record.key==='0'">
                <a-table
                      :columns="incolumns"
                      :dataSource="indata_bj"
                      :pagination="false"
                      :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
                    >
                      <span slot="status" slot-scope="text">
                        <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
                      </span>
                      <span slot="device_type" slot-scope="text,record">
                       <span v-if="record.device_type === '存储设备'">
                         <a-icon type="hdd"/> {{record.device_type}}
                       </span>
                       <span v-if="record.device_type === '灾备设备'">
                         <a-icon type="interation"/> {{record.device_type}}
                       </span>
                       <span v-if="record.device_type === '一体化设备'">
                         <a-icon type="cluster"/> {{record.device_type}}
                       </span>
                      </span>
                      <span slot="space" slot-scope="text,record">
                          {{record.space}}
                      </span>
                      <span slot="action" slot-scope="text, record">
                        <a-dropdown :trigger="['click']">
                            <a class="ant-dropdown-link" > 配置<a-icon type="down" /> </a>
                            <a-menu slot="overlay">
                              <a-menu-item key="0">
                                 <span><a @click="() => Add_Device(true)">修改信息</a></span>
                              </a-menu-item>
                              <a-menu-item key="1">
                                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                  <template slot="title">
                                    <p>是否确定删除该分组及下属所有节点？</p>
                                  </template>
                                  <span><a>删除设备</a></span>
                                </a-popconfirm>
                              </a-menu-item>
                            </a-menu>
                        </a-dropdown>
                      </span>      
                 </a-table>
              </span>
              <span v-else-if="record.key ==='1'">
                <a-table
                      :columns="incolumns"
                      :dataSource="indata_tj"
                      :pagination="false"
                      :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
                    >
                      <span slot="status" slot-scope="text">
                        <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
                      </span>
                      <span slot="device_type" slot-scope="text,record">
                       <span v-if="record.device_type === '存储设备'">
                         <a-icon type="hdd"/> {{record.device_type}}
                       </span>
                       <span v-if="record.device_type === '灾备设备'">
                         <a-icon type="interation"/> {{record.device_type}}
                       </span>
                       <span v-if="record.device_type === '一体化设备'">
                         <a-icon type="cluster"/> {{record.device_type}}
                       </span>
                      </span>
                      <span slot="space" slot-scope="text,record">
                          {{record.space}}
                      </span>
                      <span slot="action" slot-scope="text, record">
                        <a-dropdown :trigger="['click']">
                            <a class="ant-dropdown-link" > 配置<a-icon type="down" /> </a>
                            <a-menu slot="overlay">
                              <a-menu-item key="0">
                                 <span><a @click="() => Add_Device(true)">修改信息</a></span>
                              </a-menu-item>
                              <a-menu-item key="1">
                                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                  <template slot="title">
                                    <p>是否确定删除该分组及下属所有节点？</p>
                                  </template>
                                  <span><a>删除设备</a></span>
                                </a-popconfirm>
                              </a-menu-item>
                            </a-menu>
                        </a-dropdown>
                      </span>      
                 </a-table>
              </span>
              <span v-else-if="record.key ==='2'">
                <a-table style="margin-top: 8px" :columns="incolumns" :dataSource="[]" />
              </span>
            </span>
         </a-table>
      </div>
      <a-modal
      title="添加注册码"
      centered
      v-model="add_license"
      @ok="() => add_license = false"
      :width="500"
      :visible="visible"
      :confirmLoading="confirmLoading"
    >
      <template>
        <a-input placeholder="注册码" />
      </template>
    </a-modal>
    <a-modal
         title="添加设备"
         centered
         v-model="add_device"
         @ok="() => add_device = false"
         :width="500"
         :visible="visible"
       >
               <a-form :form="form">
                 <a-form-item
                   label="设备名称"
                   :labelCol="labelCol"
                   :wrapperCol="wrapperCol"
                 >
                 <a-input />
                 
                   </a-form-item>
                 <a-form-item
                   label="IP 地址"
                   :labelCol="labelCol"
                   :wrapperCol="wrapperCol"
                 >
                   <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的IP地址'}]}]" />
                 </a-form-item>
                 <a-form-item
                   label="登录用户"
                   :labelCol="labelCol"
                   :wrapperCol="wrapperCol"
                 >
                   <a-input />
                 </a-form-item>
                 <a-form-item
                   label="登录密码"
                   :labelCol="labelCol"
                   :wrapperCol="wrapperCol"
                 >
                   <a-input type="password" />
                 </a-form-item>
                 <a-form-item
                   label="管理方式"
                   :labelCol="labelCol"
                   :wrapperCol="wrapperCol"
                 >
                 <a-select
                        v-decorator="[
                          'select',
                          {rules: [{ required: true, message: '请选择管理方式' }]}
                        ]"
                        placeholder="请选择管理方式"
                      >
                        <a-select-option value="统一管理">
                          统一管理
                        </a-select-option>
                        <a-select-option value="共同管理">
                          共同管理
                        </a-select-option>
                      </a-select>
                   </a-form-item>
               </a-form>
       </a-modal>
    </a-card>
  </div>
  
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { ChartCard, MiniArea, MiniBar, MiniProgress, RankList, Bar, Trend, NumberInfo, MiniSmoothArea } from '@/components'
import { PageView, RouteView } from '@/layouts'
import { mixinDevice } from '@/utils/mixin.js'
import { getRoleList, getServiceList} from '@/api/manage'
const statusMap = {
  0: {
    status: 'default',
    text: '离线'
  },
  1: {
    status: 'success',
    text: '在线'
  },
  2: {
    status: 'processing',
    text: '异常'
  },
  3: {
    status: 'error',
    text: '故障'
  }
}
const columns = [
    {
      title: '分组名称',
      dataIndex: 'modle',
      key: 'modle',
    },
    {
      title: '设备总数',
      dataIndex: 'capacity',
      key: 'capacity',
    },
    {
      title: '组内状态',
      dataIndex: 'status',
      scopedSlots: { customRender: 'status' },
      key: 'status',
    },
   {
     title: '操作',
     dataIndex: 'action',
     width: '100px',
     scopedSlots: { customRender: 'action' }
   }
  ];
  const incolumns = [
      {
        title: '设备名称',
        dataIndex: 'device_name',
        key: 'device_name',
      },
      {
        title: '设备类别',
        dataIndex: 'device_type',
        scopedSlots: { customRender: 'device_type' },
        key: 'device_type',
      },
      {
        title: '设备状态',
        dataIndex: 'status',
        scopedSlots: { customRender: 'status' },
        key: 'status',
      },
      {
        title: '空间使用率',
        dataIndex: 'space',
        scopedSlots: { customRender: 'space' },
        key: 'space',
      },
      {
        title: '管理权限',
        dataIndex: 'role',
        scopedSlots: { customRender: 'role' },
        key: 'role',
      },
     {
       title: '操作',
       dataIndex: 'action',
       width: '100px',
       scopedSlots: { customRender: 'action' }
     }
    ];

  const data = [
    {
      key: '0',
      modle: '北京中心',
      capacity: '4',
      status: '1'
    },
    {
      key: '1',
      modle: '天津中心',
      capacity: '2',
      status: '2'
    },
    {
      key: '2',
      modle: '河南中心',
      capacity: '0',
      status: '0'
    },
  ];
  const sourceData = [
    { item: '已使用', count: 19 },
    { item: '未使用', count: 81 }
  ]
  
  const pieScale = [{
    dataKey: 'percent',
    min: 0,
    formatter: '.0%'
  }]
  const DataSet = require('@antv/data-set')
  const dv = new DataSet.View().source(sourceData)
  dv.transform({
    type: 'percent',
    field: 'count',
    dimension: 'item',
    as: 'percent'
  })
  const pieData = dv.rows
export default {
  components: {
    RouteView,
    PageView,
    ChartCard,
    MiniArea,
    MiniBar,
    MiniProgress,
    RankList,
    Bar,
    Trend,
    NumberInfo,
    MiniSmoothArea
  },
  mixins: [mixinDevice],
  data () {
    return {
      // horizontal  inline
      mode: 'inline',
        data,
        columns,
        incolumns,
      pieScale,
      pieData,
      sourceData,
      pieStyle: {
        stroke: '#fff',
        lineWidth: 1
      },
      add_group: false,
      openKeys: [],
      selectedKeys: [],
      add_license: false,
      add_device: false,
      selectedRowKeys: [],
      selectedRows: [],
      // cropper
      preview: {},
      option: {
        img: '/avatar2.jpg',
        info: true,
        size: 1,
        outputType: 'jpeg',
        canScale: false,
        autoCrop: true,
        // 只有自动截图开启 宽度高度才生效
        autoCropWidth: 180,
        autoCropHeight: 180,
        fixedBox: true,
        // 开启宽度和高度比例
        fixed: true,
        fixedNumber: [1, 1]
      },
      indata_bj:[
        {
          device_name: '产品拓展部',
          device_type: '存储设备',
          status: '1',
          role: '统一管理',
          space: '19%'
        },
        {
          device_name: '行业售后部',
          device_type: '灾备设备',
          status: '1',
          role: '统一管理',
          space: '45%'
        },
        {
          device_name: '开发1部',
          device_type: '一体化设备',
          status: '1',
          role: '共同管理',
          space: '32%'
        },
      ],
      indata_tj:[
        {
          device_name: '渠道售后部',
          device_type: '灾备设备',
          status: '1',
          role: '统一管理',
          space: '19%'
        },
        {
          device_name: '产品研发部',
          device_type: '一体化设备',
          status: '2',
          role: '共同管理',
          space: '45%'
        },
      ],
      pageTitle: ''
    }
  },
  created () {
    this.updateMenu()
  },
  computed: {
    hasSelected() {
      return this.selectedRowKeys.length > 0
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
    onOpenChange (openKeys) {
      this.openKeys = openKeys
    },
    Add_Device (add_device){
      this.add_device = add_device
    },
    Add_Group(add_group){
      this.add_group = add_group},
    updateMenu () {
      const routes = this.$route.matched.concat()
      this.selectedKeys = [ routes.pop().path ]
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
    Add_License(add_license){
      this.add_license = add_license
    },
  },
  watch: {
    '$route' (val) {
      this.updateMenu()
    }
  }
}
</script>

<style lang="less" scoped>
  .extra-wrapper {
    line-height: 55px;
    padding-right: 24px;
  
    .extra-item {
      display: inline-block;
      margin-right: 24px;
  
      a {
        margin-left: 24px;
      }
    }
  }
  .account-settings-info-main {
    width: 100%;
    display: flex;
    height: 100%;
    overflow: auto;

    &.mobile {
      display: block;

      .account-settings-info-left {
        border-right: unset;
        border-bottom: 1px solid #e8e8e8;
        width: 100%;
        height: 50px;
        overflow-x: auto;
        overflow-y: scroll;
      }
      .account-settings-info-right {
        padding: 20px 40px;
      }
    }

    .account-settings-info-left {
      border-right: 1px solid #e8e8e8;
      width: 224px;
    }

    .account-settings-info-right {
      flex: 1 1;
      padding: 8px 40px;

      .account-settings-info-title {
        color: rgba(0,0,0,.85);
        font-size: 20px;
        font-weight: 500;
        line-height: 28px;
        margin-bottom: 12px;
      }
      .account-settings-info-view {
        padding-top: 12px;
      }
    }
  }

</style>
