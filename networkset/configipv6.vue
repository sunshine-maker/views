<template>
  
  <a-card :bordered="false">
    <div class="table-operator">
     <!-- <a-dropdown>
            <a-menu slot="overlay">
              <a-menu-item key="1"><a-icon type="user" />添加聚合</a-menu-item>
              <a-menu-item key="2"><a-icon type="user" />管理聚合</a-menu-item>
            </a-menu>
            <a-button style="margin-left: 8px"> 聚合管理 <a-icon type="down" /> </a-button>
          </a-dropdown> -->
      <a-dropdown >
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="() => Add_Netm(true)"><a-icon type="plus-circle" />添加聚合</a-menu-item>
          <!-- lock | unlock -->
          <a-menu-item key="2" @click="() => Manage_Netw(true)"><a-icon type="setting" />管理聚合</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          聚合设置 <a-icon type="down" />
        </a-button>
      </a-dropdown>
      <a-dropdown v-action:edit >
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="() => Add_Netm(true)"><a-icon type="plus-circle" />添加IPMP</a-menu-item>
          <!-- lock | unlock -->
          <a-menu-item key="2" @click="() => Manage_Netw(true)"><a-icon type="setting" />管理IPMP</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          IPMP设置 <a-icon type="down" />
        </a-button>
      </a-dropdown>
    </div>
    <div>
    <a-alert message="********" banner closable />
    </div>
   <template>
     <a-table :columns="columns" :dataSource="data">
       <span slot="status" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
       <span slot="action" slot-scope="text, record">
         <span v-if="record.status==='4'">
           <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
             <template slot="title">
               <p>是否确定启用该网卡？</p>
             </template>
             <a>启用</a>
           </a-popconfirm>
         </span>
         <span v-else>
         <a-dropdown :trigger="['click']">
             <a class="ant-dropdown-link" > 配置<a-icon type="down" /> </a>
             <a-menu slot="overlay">
               <a-menu-item key="0">
                  <span><a @click="() => setNet_Ip(true)">IP地址</a></span>
               </a-menu-item>
               <a-menu-item key="1">
                 <span><a @click="() => setNet_Dns(true)">DNS</a></span>
               </a-menu-item>
              <a-menu-item key="2">
                <span v-if="record.network_router">
                  <a @click="() => modifyNet_Router(true)">修改路由</a>
                </span>
                <span v-else>
                  <span v-if="record.network_ip">
                    <span v-if="record.status==='1'">
                      <a href="javascript:;"disabled="">添加路由</a>
                    </span>
                    <span v-else>
                      <a @click="() => addNet_Router(true)">添加路由</a>
                    </span>
                  </span>
                  <span v-else>
                    <a href="javascript:;" disabled>添加路由</a>
                  </span>
                </span>
              </a-menu-item>
              <a-menu-item key="3">
                <span v-if="record.network_ip">
                  <span v-if="record.network_t">
                    <a href="javascript:;" disabled>流量控制</a>
                  </span>
                  <span v-else>
                    <span v-if="record.status==='1'">
                     <a href="javascript:;" disabled >流量控制</a>
                    </span>
                    <span v-else>
                      <a @click="() => addNet_Contrle(true)">流量控制</a>
                    </span>
                  </span>
                </span>
                <span v-else>
                  <a href="javascript:;" disabled>流量控制</a>
                </span>
              </a-menu-item>
             </a-menu>
           </a-dropdown>
         <a-modal
             title="设置XXX网卡IP地址"
             centered
             v-model="net_ip"
             @ok="() => net_ip = false"
             :width="500"
             :visible="visible"
             :confirmLoading="confirmLoading"
           >
              <a-spin :spinning="confirmLoading">
                <a-form :form="form">
                  <a-form-item
                    label="配置IPv4"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <a-select
                         v-decorator="[
                           'select',
                           {rules: [{ required: true, message: '请选择IP获取方式' }]}
                         ]"
                         placeholder="请选择IP获取方式"
                       >
                         <a-select-option value="静态">
                           静态
                         </a-select-option>
                         <a-select-option value="动态">
                           动态
                         </a-select-option>
                         <a-select-option value="禁用">
                           禁用
                         </a-select-option>
                       </a-select>
                    </a-form-item>
                  <a-form-item
                    label="IP 地址"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                    <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的IP地址'}]}]" />
                  </a-form-item>
                  <a-form-item
                    label="子网掩码"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                    <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的掩码地址'}]}]" />
                  </a-form-item>
                  <a-form-item
                    label="路由地址"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                    <a-input />
                  </a-form-item>
                </a-form>
              </a-spin>
           </a-modal>
       </span>
       </span>
     </a-table>
   </template>
   <a-modal
        title="修改路由"
        centered
        v-model="modify_router"
        @ok="() => modify_router = false"
        :width="500"
        :visible="visible"
        :confirmLoading="confirmLoading"
      >
        <template>
          <p>192.168.0.100</p>
          <a-input placeholder="新路由地址" />
        </template>
      </a-modal>
      <a-modal
           title="添加网卡聚合"
           centered
           v-model="add_netm"
           @ok="() => add_netm = false"
           :width="500"
           :visible="visible"
           :confirmLoading="confirmLoading"
         >
           <a-spin :spinning="confirmLoading">
                 <a-form :form="form">
                   <a-form-item
                     label="选择网卡"
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                   >
                    <a-table :rowSelection="rowSelection" :columns="netw_columns" :dataSource="netw_data":pagination="false":size="small">
                       <a slot="name" slot-scope="text" href="javascript:;">{{text}}</a>
                     </a-table>
                     </a-form-item>
                   <a-form-item
                     label="配置IPv4"
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                   >
                   <a-select
                          v-decorator="[
                            'select',
                            {rules: [{ required: true, message: '请选择IP获取方式' }]}
                          ]"
                          placeholder="请选择IP获取方式"
                        >
                          <a-select-option value="静态">
                            静态
                          </a-select-option>
                          <a-select-option value="动态">
                            动态
                          </a-select-option>
                          <a-select-option value="禁用">
                            禁用
                          </a-select-option>
                        </a-select>
                     </a-form-item>
                   <a-form-item
                     label="IP 地址"
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                   >
                     <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的IP地址'}]}]" />
                   </a-form-item>
                   <a-form-item
                     label="子网掩码"
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                   >
                     <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的掩码地址'}]}]" />
                   </a-form-item>
                   <a-form-item
                     label="路由地址"
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                   >
                     <a-input />
                   </a-form-item>
                   <a-form-item
                     label="LACP"
                     :labelCol="labelCol"
                     :wrapperCol="wrapperCol"
                   >
                   <a-select
                          v-decorator="[
                            'select',
                            {rules: [{ required: true, message: '请选择IP获取方式' }]}
                          ]"
                          placeholder="请选择LCP模式"
                        >
                          <a-select-option value="主动">
                            主动
                          </a-select-option>
                          <a-select-option value="被动">
                            被动
                          </a-select-option>
                          <a-select-option value="禁用">
                            禁用
                          </a-select-option>
                        </a-select>
                     </a-form-item>
                 </a-form>
               </a-spin>
         </a-modal>
    <a-modal
        title="添加路由"
        centered
        v-model="add_router"
        @ok="() => add_router = false"
        :width="500"
        :visible="visible"
        :confirmLoading="confirmLoading"
      >
        <template>
          <a-input placeholder="路由地址" />
        </template>
      </a-modal>
    <a-modal
        title="添加DNS"
        centered
        v-model="add_dns"
        @ok="() => add_dns = false"
        :width="500"
        :visible="visible"
        :confirmLoading="confirmLoading"
      >
        <template>
          <a-input placeholder="DNS地址" />
        </template>
      </a-modal>
    <a-modal
        title="设置XXX网卡IP地址"
        centered
        v-model="net_ip"
        @ok="() => net_ip = false"
        :width="500"
        :visible="visible"
        :confirmLoading="confirmLoading"
      >
         <a-spin :spinning="confirmLoading">
           <a-form :form="form">
             <a-form-item
               label="配置IPv4"
               :labelCol="labelCol"
               :wrapperCol="wrapperCol"
             >
             <a-select
                    v-decorator="[
                      'select',
                      {rules: [{ required: true, message: '请选择IP获取方式' }]}
                    ]"
                    placeholder="请选择IP获取方式"
                  >
                    <a-select-option value="静态">
                      静态
                    </a-select-option>
                    <a-select-option value="动态">
                      动态
                    </a-select-option>
                    <a-select-option value="禁用">
                      禁用
                    </a-select-option>
                  </a-select>
               </a-form-item>
             <a-form-item
               label="IP 地址"
               :labelCol="labelCol"
               :wrapperCol="wrapperCol"
             >
               <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的IP地址'}]}]" />
             </a-form-item>
             <a-form-item
               label="子网掩码"
               :labelCol="labelCol"
               :wrapperCol="wrapperCol"
             >
               <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的掩码地址'}]}]" />
             </a-form-item>
             <a-form-item
               label="路由地址"
               :labelCol="labelCol"
               :wrapperCol="wrapperCol"
             >
               <a-input />
             </a-form-item>
           </a-form>
         </a-spin>
      </a-modal>
      <a-modal
          title="设置XXX网卡DNS地址"
          centered
          v-model="net_dns"
          @ok="() => net_dns = false"
          :width="500"
          :visible="visible"
          :confirmLoading="confirmLoading"
        >
          <template>
            <a-table :columns="dns_columns" :dataSource="dns_data">
              <a slot="action">
              <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                <template slot="title">
                  <p>是否确定删除该DNS信息？</p>
                </template>
                <a>删除</a>
              </a-popconfirm>
             </a>
            </a-table>
          <a @click="() => addNet_Dns(true)"><a-icon type="plus"/>添加</a>
          </template>`
     </a-modal>
     <a-modal
          title="网卡聚合管理"
          centered
          v-model="manage_netw"
          @ok="() => manage_netw = false"
          :width="1050"
          :visible="visible"
          :confirmLoading="confirmLoading"
        >
          <template>
            <a-table :columns="juhe_columns" :dataSource="juhe_data">
              <span slot="status" slot-scope="text">
                <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
              </span>
              <span slot="network_card" slot-scope="network_card">
                    <a-tag
                      v-for="tag in network_card"
                      :color="tag==='loser' ? 'volcano' : (tag.length > 5 ? 'geekblue' : 'green')"
                      :key="tag"
                    >
                      {{tag.toUpperCase()}}
                    </a-tag>
                  </span>
              <a slot="action">
                <a-dropdown :trigger="['click']">
                    <a class="ant-dropdown-link" href="#"> 设置 <a-icon type="down" /> </a>
                    <a-menu slot="overlay">
                      <a-menu-item key="0">
                        <a @click="() => Add_Netm(true)">修改设置</a>
                      </a-menu-item>
                      <a-menu-item key="1">
                        <a @click="() => addNet_Contrle(true)">流量控制</a>
                      </a-menu-item>
                      <a-menu-item key="3">
                        <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                          <template slot="title">
                            <p>是否确定删除该DNS信息？</p>
                          </template>
                          <a>删除聚合</a>
                        </a-popconfirm>
                      </a-menu-item>
                    </a-menu>
                  </a-dropdown>
              
             </a>
            </a-table>
          </template>
     </a-modal>
   <a-modal
        title="设置XXX网卡流量控制"
        centered
        v-model="net_contrle"
        @ok="() => net_contrle = false"
        :width="500"
        :visible="visible"
        :confirmLoading="confirmLoading"
      >
        <template>
          <a-spin :spinning="confirmLoading">
                <a-form :form="form">
                  <a-form-item
                    label="优先级"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <a-select
                         v-decorator="[
                           'select',
                           {rules: [{ required: true, message: '请选择IP获取方式' }]}
                         ]"
                         placeholder="请选择Qos优先级"
                       >
                         <a-select-option value="低">
                          低
                         </a-select-option>
                         <a-select-option value="中">
                           中
                         </a-select-option>
                         <a-select-option value="高">
                           高
                         </a-select-option>
                         <a-select-option value="关闭">
                            关闭
                         </a-select-option>
                       </a-select>
                    </a-form-item>
                  <a-form-item
                    label="IP 地址"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <div>
                    <div style="float:left">
                      <a-input 
                      :value="value"
                      @change="onChange"
                      @blur="onBlur"
                      :maxLength="25"
                      style="width: 120px"
                      v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入正确的IP地址'}]}]" 
                      />
                    </div>
                    <div style="float:left;margin-left:15px">
                      <p>Mbps</p>
                    </div>
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
      net_ip: false,
      net_dns: false,
      add_dns: false,
      add_router: false,
      modify_router: false,
      net_contrle: false,
      add_netm: false,
      manage_netw: false,
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      netw_columns: [
        {
          title: '网卡',
          dataIndex: 'netw',
        }
      ],
      netw_data: [
        {
          netw: 'e1000g0'
        },
        {
          netw: 'e1000g1'
        },
        {
          netw: 'e1000g2'
        }
      ],
      dns_columns: [
        {
          title: 'DNS地址',
          dataIndex: 'DNS',
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' },
        }
      ],
      dns_data: [
        {
          DNS: '114.114.114.114'
        },
        {
          DNS: '8.8.8.8'
        },
        {
          DNS: '202.106.0.20'
        }
      ],
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
           title: 'IP地址及掩码',
           dataIndex: 'network_ip',
           scopedSlots: { customRender: 'network_ip' }
         },
         {
           title: '静态路由',
           dataIndex: 'network_router',
           scopedSlots: { customRender: 'network_router' }
         },
         {
           title: '链路状态',
           dataIndex: 'network_t',
           scopedSlots: { customRender: 'network_t' }
         },
         {
           title: '流量控制',
           dataIndex: 'network_contorl',
           scopedSlots: { customRender: 'network_contorl' }
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
           status: '4',
           network_t: '',
           network_ipmp: '',
           network_name: 'e1000g0',
           network_ip: '192.168.0.111/24',
           network_router: '192.168.0.100',
           network_contorl: '高（500Mbps)'
         },
         {
           key: '1',
           status: '0',
           network_t: '聚合（aggr1）',
           network_ipmp: '',
           network_name: 'e1000g0',
           network_ip: '192.168.0.1/24',
           network_router: '192.168.0.100',
           network_contorl: '低（300Mbps)'
         },
         {
           key: '2',
           status: '2',
           network_t: '',
           network_ipmp: '',
           network_name: 'e1000g1',
           network_ip: '192.168.1.2/24',
           network_router: '192.168.0.100',
           network_contorl: '未启用'
         },
         {
           key: '3',
           status: '1',
           network_t: '',
           network_ipmp: '',
           network_name: 'e1000g5',
           network_ip: '192.168.4.8/24',
           network_router: '',
           network_contorl: '未启用',
         },
         {
           key: '4',
           status: '0',
           network_t: '',
           network_ipmp:'',
           network_name: 'e1000g3',
           network_ip: '',
           network_router: '',
           network_contorl: '未启用'
         },
         {
           key: '5',
           status: '0',
           network_t: 'IPMP(aggr2)',
           network_ipmp:'',
           network_name: 'e1000g4',
           network_ip: '192.168.4.5/24',
           network_router: '',
           network_contorl: '未启用'
         },
         {
           key: '6',
           status: '0',
           network_t:'IPMP(aggr2)',
           network_ipmp:'',
           network_name: 'e1000g5',
           network_ip: '192.168.4.6/24',
           network_router: '',
           network_contorl: '未启用'
         },
         {
           key: '7',
           status: '0',
           network_t: '聚合（aggr1）',
           network_ipmp:'',
           network_name: 'e1000g2',
           network_ip: '192.168.2.3/24',
           network_router: '',
           network_contorl: '低（300Mbps)'
         }
       ],
       juhe_columns: [
          {
            title: '状态',
            dataIndex: 'status',
            scopedSlots: { customRender: 'status' }
          },
          {
            title: '名称',
            dataIndex: 'network_name'
          },
          {
            title: '网卡',
            dataIndex: 'network_card',
            scopedSlots: { customRender: 'network_card' }
          },
          {
            title: 'IP地址及掩码',
            dataIndex: 'network_ip',
            scopedSlots: { customRender: 'network_ip' }
          },
          {
            title: '网关',
            dataIndex: 'network_gate',
          },
          {
            title: '静态路由',
            dataIndex: 'network_router',
            scopedSlots: { customRender: 'network_router' }
          },
          {
            title: '模式',
            dataIndex: 'network_t',
            scopedSlots: { customRender: 'network_t' }
          },
          {
            title: '流量控制',
            dataIndex: 'network_contorl',
            scopedSlots: { customRender: 'network_contorl' }
          },
          {
            title: '操作',
            dataIndex: 'action',
            width: '80px',
            scopedSlots: { customRender: 'action' }
          }
        ],
       juhe_data: [
          {
            key: '0',
            status: '1',
            network_t: '主动',
            network_card: ['e1000g0','e1000g1'],
            network_name: 'aggr1',
            network_ip: '192.168.0.111/24',
            network_gare: '',
            network_router: '192.168.0.100',
            network_contorl: '高（500Mbps)'
          }
        ],
      setNet_Ip(net_ip){
        this.net_ip = net_ip},
      setNet_Dns(net_dns){
        this.net_dns = net_dns},
      addNet_Dns(add_dns){
        this.add_dns = add_dns},
      addNet_Router(add_router){
        this.add_router = add_router},
      modifyNet_Router(modify_router){
        this.modify_router = modify_router},
      addNet_Contrle (net_contrle){
        this.net_contrle = net_contrle
      },
      Add_Netm (add_netm){
        this.add_netm = add_netm
      },
      Manage_Netw(manage_netw){
        this.manage_netw = manage_netw
      },
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
