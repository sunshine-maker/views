<template>
  <a-card :bordered="false">
    <div>
      <a-alert message="在未加入域时仅显示加入域的选项(下方内容)" banner /> 
    </div>
    <template>
      <div style="text-align:center">
        <a-form :form="form">
          <a-form-item>
            <a-row>
              <a-col :sm="5" :xs="24">
                  <span>DNS服务器</span>
              </a-col>
              <a-col :sm="7" :xs="24">
                <a-input />
              </a-col>  
            </a-row>
          </a-form-item>
          <a-form-item>
            <a-row>
              <a-col :sm="5" :xs="24">
                  <span>AD域名</span style="float:left;">
              </a-col>
              <a-col :sm="7" :xs="24">
                <a-input />
              </a-col>  
            </a-row>
          </a-form-item>
          <a-form-item>
            <a-row>
              <a-col :sm="5" :xs="24">
                  <span>AD链接用户</span>
              </a-col>
              <a-col :sm="7" :xs="24">
                <a-input />
              </a-col>  
            </a-row>
          </a-form-item>
          <a-form-item>
            <a-row>
              <a-col :sm="5" :xs="24">
                  <span>AD链接密码</span>
              </a-col>
              <a-col :sm="7" :xs="24">
                <a-input type="password"/>
              </a-col>  
            </a-row>
          </a-form-item>
          <a-form-item>
            <a-row>
              <a-col :sm="5" :xs="24">
                  <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
              </a-col>
              <a-col :sm="7" :xs="24">
                <span>兼容 Windows Server 2008 及以上操作系统</span>
              </a-col>  
            </a-row>
          </a-form-item>
          <a-form-item :wrapper-col="{ span: 12, offset: 5 }">
            <a-button type="primary" html-type="submit">
              加入域
            </a-button>
          </a-form-item>
        </a-form>
      </div>
    </template>
    <a-divider/>
    <div>
      <a-alert message="在加入域后仅显示下方内容" banner /> 
    </div>
   <template>
     <a-table :columns="columns" :dataSource="data" class="components-table-demo-nested">
      <span slot="domainmap" slot-scope="text">
        <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
      </span>
      <span slot="action" slot-scope="text, record">
        <a-dropdown :trigger="['click']">
           <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
           <a-menu slot="overlay">
             <a-menu-item key="0">
              <span v-if="record.domainmap === '1'">
                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="() => Add_Map(true)">
                  <template slot="title">
                    <p>执行手动映射需要先执行关闭自动映射！</p>
                    <p>是否确认修改该用户映射？</p>
                    <a-checkbox>确认修改</a-checkbox>
                  </template>
                  <span>手动映射</span>
                </a-popconfirm>
              </span>
              <span v-else>
                <a @click="() => Add_Map(true)"><span>手动映射</span></a>
              </span>
             </a-menu-item>
             <a-menu-item key="1">
               <a-popconfirm placement="left" okText="Yes" cancelText="No">
                 <template slot="title">
                   <p>是否确认创建自动映射？</p>
                   <a-checkbox>确认创建</a-checkbox>
                 </template>
                 <span>自动映射</span>
               </a-popconfirm>
             </a-menu-item>
             <a-menu-item key="2">
              <span v-if="record.domainmap === '1'">
                <a @click="() => Auto_Map(true)"><span>查看映射</span></a>
              </span>
              <span v-if="record.domainmap < '1'">
                <a @click="() => Show_Map(true)"><span>查看映射</span></a>
              </span>
              <span v-if="record.domainmap > '1'">
                <a disabled="true"><span>查看映射</span></a>
              </span>
             </a-menu-item>
             <a-menu-item key="3">
               <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
              <template slot="title">
                <p>是否确定退出域？</p>
                <a-checkbox>确认退出域</a-checkbox>
              </template>
              <span>退出域</span>
            </a-popconfirm></a>
             </a-menu-item>
           </a-menu>
         </a-dropdown>
         <a-modal
             title="添加映射"
             centered
             v-model="add_map"
             @ok="() => add_map = false"
             :width="500"
             :visible="visible"
             :confirmLoading="confirmLoading"
           >
              <a-spin :spinning="confirmLoading">
                <a-form :form="form">
                  <a-form-item>
                    <a-row>
                      <a-col :sm="10" :xs="24">
                        <a-select defaultValue="Windows域用户" style="width: 175px">
                          <a-select-option value="WindowsAD">Windows域用户</a-select-option>
                        </a-select>
                      </a-col>
                      <a-col :sm="10" :xs="24">
                        <a-select defaultValue="Administrator@mars.com" style="width: 175px">
                          <a-select-option value="Ad">Administrator@mars.com</a-select-option>
                        </a-select>
                      </a-col>
                    </a-row> 
                    <a-divider>↓</a-divider>
                    <a-row>
                      <a-col :sm="10" :xs="24">
                        <a-select defaultValue="NAS用户" style="width: 175px">
                          <a-select-option value="NAS">NAS用户</a-select-option>
                        </a-select>
                      </a-col>
                      <a-col :sm="10" :xs="24">
                        <a-select defaultValue="test1" style="width: 175px">
                          <a-select-option value="test1">test1</a-select-option>
                        </a-select>
                      </a-col>
                    </a-row>
                  </a-form-item>
                </a-form>
              </a-spin>
           </a-modal>
         <a-modal
             title="查看手动映射"
             centered
             v-model="show_map"
             @ok="() => show_map = false"
             :width="500"
             :visible="visible"
             :confirmLoading="confirmLoading"
           >
              <a-spin :spinning="confirmLoading">
                <a-form :form="form">
                  <a-form-item>
                    <a-table :columns="map_columns" :dataSource="map_data" :pagination="false" class="components-table-demo-nested">
                      <span slot="action" slot-scope="text, record">
                        <a-popconfirm placement="left" okText="Yes" cancelText="No">
                          <template slot="title">
                            <p>是否确认删除该映射？</p>
                            <a-checkbox>确认删除</a-checkbox>
                          </template>
                          <a>删除</a>
                        </a-popconfirm>
                      </span>
                    </a-table>
                 </a-form-item>
              </a-form>
            </a-spin>
         </a-modal>
         <a-modal
             title="查看自动映射"
             centered
             v-model="auto_map"
             @ok="() => auto_map = false"
             :width="700"
             :visible="visible"
             :confirmLoading="confirmLoading"
           >
            
            <div>
              <a-button>关闭自动映射</a-button>
            </div>
            <a-table :columns="auto_columns" :dataSource="auto_data" class="components-table-demo-nested">
              <span slot="status" slot-scope="text">
                <a-badge :status="text | statusadTypeFilter" :text="text | statusadFilter" />
              </span>
              <span slot="action" slot-scope="text, record">
                <span v-if="record.status==='1'">
                  <a-popconfirm placement="left" okText="Yes" cancelText="No">
                    <template slot="title">
                      <p>是否确认添加该映射？</p>
                      <a-checkbox>确认添加</a-checkbox>
                    </template>
                    <a>添加映射</a>
                  </a-popconfirm>
                </span>  
                <span v-else>
                  <a-popconfirm placement="left" okText="Yes" cancelText="No">
                    <template slot="title">
                      <p>是否确认解除该映射？</p>
                      <a-checkbox>确认解除</a-checkbox>
                    </template>
                    <a>解除映射</a>
                  </a-popconfirm>
                </span>  
              </span>
            </a-table>
           </a-modal>
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
    text: '已手动映射'
  },
  1: {
    status: 'success',
    text: '已自动映射'
  },
  2: {
    status: 'default',
    text: '未映射'
  },
}
const statusadMap = {
  0: {
    status: 'success',
    text: '已映射'
  },
  1: {
    status: 'default',
    text: '未映射'
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
      add_map: false,
      show_map: false,
      auto_map: false,
      selectedRowKeys: [],
      selectedRows: [],
      openKeys: [],
      selectedKeys: [],
      mockData: [],
      targetKeys: [],
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      map_columns: [
        {
          title: 'Windows账户',
          dataIndex: 'Windows',
        },
        {
          title: ' ',
          dataIndex: 'to',
        },
        {
          title: 'NAS账户',
          dataIndex: 'Nas',
        },
        {
          title: '操作',
          dataIndex: 'action',
          scopedSlots: { customRender: 'action' },
        }
      ],
      map_data: [
        {
          Windows: 'Administrator@mars.com',
          to: '=>',
          Nas: 'test1',
        },
      ],
      auto_columns: [
        {
          title: '域用户ID',
          dataIndex: 'domainid',
        },
        {
          title: '映射状态',
          dataIndex: 'status',
           scopedSlots: { customRender: 'status' }
        },
         {
           title: '操作',
           dataIndex: 'action',
           width: '100px',
           scopedSlots: { customRender: 'action' }
         }
      ],
      auto_data: [
        {
          domainid: '123456',
          status: '0'
        },
        {
          domainid: 'Mars',
          status: '0'
        },
        {
          domainid: 'TEST',
          status: '0'
        },
        {
          domainid: 'SQLServer2005',
          status: '1'
        },
      ],
     
      
      columns: [
         {
           title: '域名',
           dataIndex: 'domainname',
         },
         {
           title: '域控制器',
           dataIndex: 'domainip'
         },
         {
           title: 'ID映射',
           dataIndex: 'domainmap',
           scopedSlots: { customRender: 'domainmap' }
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
           domainname: 'mars.com',
           domainmap: '1',
           // 修改domainmap的值修改菜单跳转页面
           domainip: '192.168.0.100'
         }
       ],
      Show_Map(show_map){
        this.show_map = show_map;
      },
      Auto_Map(auto_map){
        this.auto_map = auto_map;
      },
      Add_Map(add_map){
        this.add_map = add_map},
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
   },
   statusadFilter (type) {
     return statusadMap[type].text
   },
   statusadTypeFilter (type) {
     return statusadMap[type].status
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
        label: customLabel, 
        value: item.title, 
      };
    },
    
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
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    }
  }
}
</script>
