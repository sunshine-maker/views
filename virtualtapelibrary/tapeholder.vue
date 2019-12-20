<template>
  <a-card :bordered="false">
    <div class="table-operator" style="margin-bottom: 10px;">
      <a-button type="primary" icon="plus" @click="() => add_Group(true)">新建磁带架</a-button>
      <a-modal
         title="新建磁带架"
         centered
         v-model="add_group"
         @ok="() => add_group = false"
          :width="500"
          :visible="visible"
        >
        <a-form :form="form">
          <a-form-item>
            <a-row>
              <a-col :sm="7" :xs="24">
                <span>磁带架名称</span>
              </a-col>
              <a-col :sm="10" :xs="24">
                <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入主机组名称'}]}]" />
              </a-col>  
            </a-row>
          </a-form-item>
        </a-form>
      </a-modal>
    </div>
    <div style="margin-bottom: 10px;">
    <a-alert message="********" banner closable />
    </div>
    <template>
      <a-table :columns="columns" :dataSource="data">
        <span slot="action" slot-scope="text, record">
          <a-dropdown :trigger="['click']">
            <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
            <a-menu slot="overlay">
              <a-menu-item key="0">
                <a @click="() => mv_Tapes(true)"><span>移动磁带</span></a>
              </a-menu-item>
              <a-menu-item key="1">
                <a @click="() => Tapes(true)"><span>磁带入库</span></a>
              </a-menu-item>
              <a-menu-item key="2">
                <a @click="() => list_Tapes(true)"><span>磁带清单</span></a>
                <a-modal
                      title="磁带入库"
                      centered
                      v-model="list_tapes"
                      @ok="() => list_tapes = false"
                      :width="500"
                      :visible="visible"
                      :confirmLoading="confirmLoading"
                   >
                  <a-spin :spinning="confirmLoading">
                    <a-form :form="form">
                      <a-form-item>
                        <a-table :columns="in_columns" :dataSource="in_data" />
                      </a-form-item>
                    </a-form>
                  </a-spin>
                </a-modal>
              </a-menu-item>
              <a-menu-item key="3">
                <span v-if="record.key < '2'">
                  <a disabled="true">删除磁带架</a>
                </span>
                <span v-else>
                    <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                      <template slot="title">
                        <p>是否确定删除磁带架？</p>
                        <a-checkbox>确认删除磁带架</a-checkbox>
                      </template>
                      <span>删除磁带架</span>
                    </a-popconfirm></a>
                </span>
              </a-menu-item>
              <a-modal
                    title="磁带入库"
                    centered
                    v-model="tapes"
                    @ok="() => tapes = false"
                    :width="700"
                    :visible="visible"
                    :confirmLoading="confirmLoading"
                 >
                 <a-spin :spinning="confirmLoading">
                   <a-form :form="form">
                     <a-form-item>
                       <a-row>
                         <a-col :sm="4" :xs="24">
                           <span>剩余槽位</span>
                         </a-col>
                         <a-col :sm="6" :xs="24">
                           <span>17</span>
                         </a-col>  
                       </a-row>
                     </a-form-item>
                     <a-form-item>
                       <a-row>
                         <a-col :sm="4" :xs="24">
                           <span>选择磁带库</span>
                         </a-col>
                         <a-col :sm="10" :xs="24">
                           <a-select defaultValue="tttt" >
                             <a-select-option value="tttt">tttt</a-select-option>
                           </a-select>
                         </a-col>  
                       </a-row>
                     </a-form-item>
                     <a-form-item>
                       <a-row>
                         <a-col :sm="30" :xs="24">
                           <a-transfer
                               :dataSource="mockData"
                               :listStyle="{
                                 width: '300px',
                                 height: '300px',
                               }"
                               :targetKeys="targetKeys"
                               @change="handleChange"
                               :render="renderItem"
                             >
                           </a-transfer>
                         </a-col>  
                       </a-row>
                     </a-form-item>
                   </a-form>
                 </a-spin>
              </a-modal>
              <a-modal
                    title="移动XXX磁带架上的磁带"
                    centered
                    v-model="mv_tapes"
                    @ok="() => mv_tapes = false"
                    :width="700"
                    :visible="visible"
                    :confirmLoading="confirmLoading"
                 >
                 <a-spin :spinning="confirmLoading">
                   <a-form :form="form">
                     <a-form-item>
                       <a-row>
                         <a-col :sm="4" :xs="24">
                           <span>目标磁带架</span>
                         </a-col>
                         <a-col :sm="10" :xs="24">
                           <a-select defaultValue="空白带架" >
                             <a-select-option value="空白带架">空白带架</a-select-option>
                             <a-select-option value="离线带架">离线带架</a-select-option>
                             <a-select-option value="tttt">tttt</a-select-option>
                           </a-select>
                         </a-col>  
                       </a-row>
                     </a-form-item>
                     <a-form-item>
                       <a-row>
                         <a-col :sm="30" :xs="24">
                           <a-transfer
                               :dataSource="mockData"
                               :listStyle="{
                                 width: '300px',
                                 height: '300px',
                               }"
                               :targetKeys="targetKeys"
                               @change="handleChange"
                               :render="renderItem"
                             >
                           </a-transfer>
                         </a-col>  
                       </a-row>
                     </a-form-item>
                   </a-form>
                 </a-spin>
              </a-modal>
            </a-menu>
          </a-dropdown>
        </span>
             title="修改XXX磁带库属性"
             centered
             v-model="add_member"
             @ok="() => add_member = false"
             :width="500"
             :visible="visible"
             :confirmLoading="confirmLoading"
           >
              <a-spin :spinning="confirmLoading">
                <a-form :form="form">
                  <a-form-item
                    label="驱动器数"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <a-input/>
                </a-form-item>
                  <a-form-item
                    label="出入槽位数"
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                  >
                  <a-input/>
                </a-form-item> 
                     <a-form-item
                       label="槽位数"
                       :labelCol="labelCol"
                       :wrapperCol="wrapperCol"
                     >
                     <a-input/>
                   </a-form-item>
                </a-form>
              </a-spin>
           </a-modal>
           <a-modal
               title="XXX磁带库详细信息"
               centered
               v-model="show_pro"
               @ok="() => show_pro = false"
               :width="700"
               :visible="visible"
               :confirmLoading="confirmLoading"
             >
                <a-spin :spinning="confirmLoading">
                  <a-form :form="form">
                    <a-form-item>
                      <a-table :columns="pro_columns" :dataSource="pro_data" :pagination="false" class="components-table-demo-nested">
                         <span slot="other" slot-scope="tags">
                              <a-tag
                                v-for="tag in tags"
                                :color="tag==='loser' ? 'volcano' : (tag.length > 5 ? 'geekblue' : 'green')"
                                :key="tag"
                              >
                                {{tag.toUpperCase()}}
                              </a-tag>
                            </span>
                      </a-table>
                      </a-form-item>
                  </a-form>
                </a-spin>
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
    text: '已连接'
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
      add_group: false,
      list_tapes: false,
      tapes: false,
      mv_tapes: false,
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
      in_columns: [
        {
          title: '磁带条码',
          dataIndex: 'tapenum',
        },
        {
          title: '总容量',
          dataIndex: 'tapecap',
        },
        {
          title: '已用空间',
          dataIndex: 'tapeused',
        },
      ],
      in_data: [
        {
          key: '0',
          tapenum: 'TAPE100000',
          tapecap: '10.0GB',
          tapeused: '2.03MB'
        },
        {
          key: '1',
          tapenum: 'TAPE100001',
          tapecap: '10.0GB',
          tapeused: '2.03MB'
        },
        {
          key: '2',
          tapenum: 'TAPE100002',
          tapecap: '10.0GB',
          tapeused: '2.03MB'
        },
      ],
      columns: [
        {
          title: '磁带架名称',
          dataIndex: 'tapename',
        },
        {
          title: '磁带数量',
          dataIndex: 'type_num'
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
          tapename: '空白带架',
          type_num: '3'
        },
        {
          key: '1',
          tapename: '离线带架',
          type_num: '3'
        },
        {
          key: '2',
          tapename: 'ttttt',
          type_num: '3'
        }
      ],
       list_Tapes(list_tapes){
         this.list_tapes = list_tapes;
       },
       Tapes(tapes){
         this.tapes = tapes;
       },
       show_Pro(show_pro){
         this.show_pro = show_pro;
       },
       mv_Tapes(mv_tapes){
         this.mv_tapes = mv_tapes;
       },
      add_Group(add_group){
        this.add_group = add_group},
      add_Member(add_member){
        this.add_member = add_member},
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
