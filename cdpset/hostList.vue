<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="主机IP">
              <a-input v-model="queryParam.id" placeholder=""/>
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="保护状态">
              <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                <a-select-option value="0">正在保护</a-select-option>
                <a-select-option value="1">降级</a-select-option>
                <a-select-option value="2">链接已断开</a-select-option>
                <a-select-option value="3">未启用保护</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="!advanced && 8 || 32" :sm="24">
            <span class="table-page-search-submitButtons" :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
              <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
              <a-button style="margin-left: 8px" @click="() => queryParam = {}">重置</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <div class="table-operator" style="margin-bottom: 10px;">
      <a-button type="primary" @click="() => add_Host(true)"><a-icon type="plus" />添加主机</a-button>
        <a-modal
            title="添加主机"
            centered
            v-model="add_host"
            @ok="() => add_host = false"
            :width="500"
            :visible="visible"
          >
           <a-form :form="form">
             <a-form-item>
               <a-row :gutter="20">
                 <a-col :sm="5" :xs="24">
                   <span>主机名称</span>
                 </a-col>
                 <a-col :sm="10" :xs="24">
                   <a-input />
                 </a-col>  
                 <a-col :sm="5" :xs="24" />
               </a-row>
             </a-form-item>
             <a-form-item>
               <a-row :gutter="20">
                 <a-col :sm="5" :xs="24">
                   <span>主机IP</span>
                 </a-col>
                 <a-col :sm="10" :xs="24">
                   <a-input />
                 </a-col>  
                 <a-col :sm="5" :xs="24" />
               </a-row>
             </a-form-item>
             <a-form-item>
               <a-row :gutter="20">
                 <a-col :sm="5" :xs="24">
                   <span>端口号</span>
                 </a-col>
                 <a-col :sm="10" :xs="24">
                   <a-input-number :min="1" :max="65526" :defaultValue="1100" />
                 </a-col>  
                 <a-col :sm="5" :xs="24" />
               </a-row>
             </a-form-item>
             <a-form-item>
               <a-row :gutter="20">
                 <a-col :sm="5" :xs="24">
                   <span>系统类别</span>
                 </a-col>
                 <a-col :sm="10" :xs="24">
                   <a-select defaultValue="Windows" >
                     <a-select-option value="Windows">Windows</a-select-option>
                     <a-select-option value="Linux">Linux</a-select-option>
                     <a-select-option value="Unix">Unix</a-select-option>
                   </a-select>
                 </a-col>  
                 <a-col :sm="5" :xs="24" />
               </a-row>
             </a-form-item>
           </a-form>
         </a-modal>
      <a-popconfirm placement="right" okText="Yes" cancelText="No" @confirm="confirm">
        <template slot="title">
          <p>是否确定扫描主机？</p>
          <a-checkbox>扫描主机</a-checkbox>
        </template>
        <a-button type="primary" style="margin-left: 8px"><a-icon type="scan" />扫描主机</a-button>
      </a-popconfirm>
    </div>
    <div style="margin-bottom: 10px;">
    <a-alert message="********" banner closable />
    </div>
   <template>
     <a-table :columns="columns" :dataSource="data" class="components-table-demo-nested">
       <span slot="status" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
       <span slot="portect" slot-scope="tags">
         <a-tag
           v-for="tag in tags"
           :color="tag==='loser' ? 'volcano' : (tag.length > 5 ? 'geekblue' : 'green')"
           :key="tag"
         >
        {{tag.toUpperCase()}}
         </a-tag>
       </span>
       <span slot="action" slot-scope="text, record">
         <a-dropdown :trigger="['click']">
           <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
           <a-menu slot="overlay">
             <a-menu-item key="0">
                <a @click="() => add_Host(true)"><span>修改主机</span></a>
             </a-menu-item>
             <a-menu-item key="2">
               <a @click="() => mirror_Manage(true)"><span>镜像管理</span></a>
               <a-modal
                   title="XXXX主机镜像管理"
                   centered
                   v-model="mirror_manage"
                   @ok="() => mirror_manage = false"
                   :width="600"
                   :visible="visible"
                 >
                 <a-table :columns="disk_columns" :dataSource="disk_data" class="components-table-demo-nested">
                   <span slot="action" slot-scope="text, record">
                     <span v-if="record.mirrordisk === '磁盘2*'">
                       <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                         <template slot="title">
                           <p>是否确定<b style="color:red">解除</b>镜像关系？</p>
                           <p>并同时<b style="color:red">删除</b>相关保护任务？</p>
                           <p>同步<b style="color:red">清除</b>历史数据</p>
                           <a-checkbox>解除镜像</a-checkbox>
                         </template>
                         <a>解除镜像</a>
                       </a-popconfirm>
                     </span>
                     <span v-else-if="record.mirrordisk === '-'" />
                     <span v-else> 
                      <a @click="() => set_Mirror(true)"><span>设置镜像</span></a>
                      <a-modal
                          title="设置XXX磁盘/分区镜像"
                          centered
                          v-model="mirror_manage"
                          @ok="() => mirror_manage = false"
                          :width="600"
                          :visible="visible"
                        >
                        <span>目标磁盘容量：10.0GB</span>
                        <a-form>
                          <a-form-item>
                            <a-row :gutter="20">
                              <a-col :md="2" :sm="24">
                                <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
                              </a-col>
                              <a-col :md="18" :sm="24">
                                立即执行任务
                              </a-col>
                            </a-row>
                          </a-form-item>
                          <a-form-item>
                            <a-row :gutter="20">
                              <a-col :md="2" :sm="24">
                                <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
                              </a-col>
                              <a-col :md="18" :sm="24">
                                快速复制
                              </a-col>
                            </a-row>
                          </a-form-item>
                        <a-alert message="仅在目标系统为Windows时显示以下内容" banner closable />
                          <a-form-item>
                            <a-row :gutter="20">
                              <a-col :md="2" :sm="24">
                                <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
                              </a-col>
                              <a-col :md="8" :sm="24">
                                客户端重启后自动执行保护
                              </a-col>
                              <a-col :md="9" :sm="24">
                                <a-select defaultValue="重新同步" >
                                  <a-select-option value="重新同步">重新同步</a-select-option>
                                  <a-select-option value="继续保护">继续保护</a-select-option>
                                </a-select>
                              </a-col>
                              <a-col :sm="1" :xs="24" >
                                <a-popover placement="right">
                                  <template slot="content">
                                    <p>重启后数据保护的方式</p>
                                  </template>
                                  <a-icon type="question-circle" />
                                </a-popover>
                              </a-col>
                            </a-row>
                          </a-form-item>
                          <a-form-item>
                            <a-row :gutter="20">
                              <a-col :sm="5" :xs="24">
                                <span>保护等级</span>
                              </a-col>
                              <a-col :sm="10" :xs="24">
                                <a-select defaultValue="记录级" >
                                  <a-select-option value="记录级">记录级</a-select-option>
                                  <a-select-option value="快照级">快照级</a-select-option>
                                  <a-select-option value="无">无</a-select-option>
                                </a-select>
                              </a-col>  
                              <a-col :sm="5" :xs="24" />
                            </a-row>
                          </a-form-item>
                          <a-alert message="选择记录级是显示如下内容" banner closable />
                          <a-form-item>
                            <a-row :gutter="20">
                              <a-col :sm="5" :xs="24">
                                <span>记录池</span>
                              </a-col>
                              <a-col :sm="5" :xs="24">
                                <a-input-number
                                  :defaultValue="1"
                                  :min="0"
                                  :max="100000"
                                  :formatter="value => `${value}GB`"
                                  :parser="value => value.replace('GB', '')"
                                  @change="onChange"
                                />
                              </a-col>  
                              <a-col :sm="9" :xs="24" >
                               <a-select defaultValue="自动循环空间" >
                                 <a-select-option value="自动循环空间">自动循环空间</a-select-option>
                                 <a-select-option value="满后写保护">满后写保护</a-select-option>
                                 <a-select-option value="满后停止任务">满后停止任务</a-select-option>
                               </a-select>
                              </a-col>
                              <a-col :sm="1" :xs="24" >
                                <a-popover placement="right">
                                  <template slot="content">
                                    <p>记录池写满后的操作方式</p>
                                  </template>
                                  <a-icon type="question-circle" />
                                </a-popover>
                              </a-col>
                            </a-row>
                          </a-form-item>
                          <a-form-item>
                            <a-row :gutter="20">
                              <a-col :sm="5" :xs="24">
                                <span>一致性代理</span>
                              </a-col>
                              <a-col :sm="14" :xs="24">
                                <a-select defaultValue="不使用代理" >
                                  <a-select-option value="不使用代理">不使用代理</a-select-option>
                                  <a-select-option value="默认代理">使用默认代理</a-select-option>
                                  <a-select-option value="Oracle代理">Oracle代理</a-select-option>
                                  <a-select-option value="SQLServer代理">SQLServer代理</a-select-option>
                                </a-select>
                              </a-col>  
                              <a-col :sm="1" :xs="24" >
                                <a-popover placement="right">
                                  <template slot="content">
                                    <p>用于保证保护目标数据的一致性</p>
                                  </template>
                                  <a-icon type="question-circle" />
                                </a-popover>
                              </a-col>
                             </a-row>
                          </a-form-item>
                           <a-alert message="选择不使用代理不显示内容" type="info" />
                           <a-alert message="选择默认代理显示如下内容" type="info" />
                           <a-form-item>
                             <a-row :gutter="20">
                               <a-col :sm="5" :xs="24">
                                 <span>快照间隔</span>
                               </a-col>
                               <a-col :sm="5" :xs="24">
                                 <a-input-number
                                   :defaultValue="1"
                                   :min="0"
                                   :max="100000"
                                   :formatter="value => `${value}分钟`"
                                   :parser="value => value.replace('分钟', '')"
                                   @change="onChange"
                                 />
                               </a-col> 
                             </a-row>
                           </a-form-item> 
                           <a-alert message="选择使用非默认代理时显示如下公共内容" type="info" />
                           <a-form-item>
                             <a-row :gutter="20">
                               <a-col :sm="5" :xs="24">
                                 <span>代理端IP</span>
                               </a-col>
                               <a-col :sm="7" :xs="24">
                                 <a-input />
                               </a-col> 
                               <a-col :sm="4" :xs="24">
                                 <span>端口号</span>
                               </a-col>
                               <a-col :sm="4" :xs="24">
                                 <a-input-number :min="1" :max="65526" :defaultValue="41000" />
                               </a-col> 
                             </a-row>
                           </a-form-item> 
                           <a-form-item>
                             <a-row :gutter="20">
                               <a-col :sm="5" :xs="24">
                                 <span>数据库IP</span>
                               </a-col>
                               <a-col :sm="7" :xs="24">
                                 <a-input />
                               </a-col> 
                               <a-col :sm="4" :xs="24">
                                 <span>快照间隔</span>
                               </a-col>
                               <a-col :sm="4" :xs="24">
                                 <a-input-number
                                       :defaultValue="1"
                                       :min="0"
                                       :max="100000"
                                       :formatter="value => `${value}秒`"
                                       :parser="value => value.replace('秒', '')"
                                       @change="onChange"
                                     />
                               </a-col> 
                             </a-row>
                           </a-form-item> 
                           <a-alert message="选择Oracle代理显示如下内容" type="info" />
                           <a-form-item>
                             <a-row :gutter="20">
                               <a-col :sm="5" :xs="24">
                                 <span>数据库端口</span>
                               </a-col>
                               <a-col :sm="14" :xs="24">
                                   <a-input-number :min="1" :max="65526" :defaultValue="1521" />
                               </a-col> 
                               <a-col :sm="1" :xs="24">
                                 <a-popover placement="right">
                                   <template slot="content">
                                     <p>需要与当前数据库访问端口一致</p>
                                   </template>
                                   <a-icon type="question-circle" />
                                 </a-popover>
                               </a-col>
                             </a-row>
                           </a-form-item>
                            <a-form-item>
                              <a-row :gutter="20">
                                <a-col :sm="5" :xs="24">
                                  <span>数据库实例</span>
                                </a-col>
                                <a-col :sm="14" :xs="24">
                                    <a-input />
                                </a-col> 
                                <a-col :sm="1" :xs="24">
                                  <a-popover placement="right">
                                    <template slot="content">
                                      <p>需要与当前数据库使用实例一致</p>
                                    </template>
                                    <a-icon type="question-circle" />
                                  </a-popover>
                                </a-col>
                              </a-row>
                            </a-form-item>
                            <a-form-item>
                              <a-row :gutter="20">
                                <a-col :sm="5" :xs="24">
                                  <span>用户名</span>
                                </a-col>
                                <a-col :sm="14" :xs="24">
                                    <a-input />
                                </a-col> 
                                <a-col :sm="1" :xs="24">
                                  <a-popover placement="right">
                                    <template slot="content">
                                      <p>具有dba，sysdba权限的非"sys"/"system"账户</p>
                                    </template>
                                    <a-icon type="question-circle" />
                                  </a-popover>
                                </a-col>
                              </a-row>
                            </a-form-item>
                            <a-form-item>
                              <a-row :gutter="20">
                                <a-col :sm="5" :xs="24">
                                  <span>密  码</span>
                                </a-col>
                                <a-col :sm="14" :xs="24">
                                    <a-input type="password"/>
                                </a-col> 
                                <a-col :sm="1" :xs="24">
                                </a-col>
                              </a-row>
                            </a-form-item>
                            <a-alert message="选择SQLServer代理显示如下内容" type="info" />
                            <a-form-item>
                             <a-form-item>
                               <a-row :gutter="20">
                                 <a-col :sm="5" :xs="24">
                                   <span>数据库实例</span>
                                 </a-col>
                                 <a-col :sm="14" :xs="24">
                                     <a-input />
                                 </a-col> 
                                 <a-col :sm="1" :xs="24">
                                   <a-popover placement="right">
                                     <template slot="content">
                                       <p>需要与当前数据库使用实例一致</p>
                                     </template>
                                     <a-icon type="question-circle" />
                                   </a-popover>
                                 </a-col>
                               </a-row>
                             </a-form-item>
                              <a-row :gutter="20">
                                <a-col :sm="5" :xs="24">
                                  <span>数据库名称</span>
                                </a-col>
                                <a-col :sm="14" :xs="24">
                                    <a-input />
                                </a-col> 
                                <a-col :sm="1" :xs="24">
                                  <a-popover placement="right">
                                    <template slot="content">
                                      <p>需要与当前数据库名称一致</p>
                                    </template>
                                    <a-icon type="question-circle" />
                                  </a-popover>
                                </a-col>
                              </a-row>
                            </a-form-item>
                            <a-form-item>
                              <a-row :gutter="20">
                                <a-col :sm="5" :xs="24">
                                  <span>验证模式</span>
                                </a-col>
                                <a-col :sm="14" :xs="24">
                                    <a-select defaultValue="混合验证" >
                                      <a-select-option value="混合验证">混合验证</a-select-option>
                                      <a-select-option value="Windows验证">Windows验证</a-select-option>
                                    </a-select>
                                </a-col> 
                                <a-col :sm="1" :xs="24">
                                  <a-popover placement="right">
                                    <template slot="content">
                                      <p>与数据库设置一致</p>
                                    </template>
                                    <a-icon type="question-circle" />
                                  </a-popover>
                                </a-col>
                              </a-row>
                            </a-form-item>
                            <a-alert message="选择混合验证时显示账户&密码框" type="success" />
                             <a-form-item>
                               <a-row :gutter="20">
                                 <a-col :sm="5" :xs="24">
                                   <span>用户名</span>
                                 </a-col>
                                 <a-col :sm="14" :xs="24">
                                     <a-input />
                                 </a-col> 
                                 <a-col :sm="1" :xs="24">
                                   <a-popover placement="right">
                                     <template slot="content">
                                       <p>具有“sa”权限账户，可以直接填“sa“</p>
                                     </template>
                                     <a-icon type="question-circle" />
                                   </a-popover>
                                 </a-col>
                               </a-row>
                             </a-form-item>
                             <a-form-item>
                               <a-row :gutter="20">
                                 <a-col :sm="5" :xs="24">
                                   <span>密  码</span>
                                 </a-col>
                                 <a-col :sm="14" :xs="24">
                                     <a-input type="password"/>
                                 </a-col> 
                                 <a-col :sm="1" :xs="24">
                                 </a-col>
                               </a-row>
                             </a-form-item>
                          <a-alert message="选择快照级是显示如下内容" banner closable />
                          <a-form-item>
                            <a-row :gutter="20">
                              <a-col :sm="4" :xs="24">
                                <span>快照间隔</span>
                              </a-col>
                              <a-col :sm="6" :xs="24">
                                <a-input-number
                                      :defaultValue="1"
                                      :min="0"
                                      :max="100000"
                                      :formatter="value => `${value}分钟`"
                                      :parser="value => value.replace('分钟', '')"
                                      @change="onChange"
                                    />
                              </a-col>  
                              <a-col :sm="4" :xs="24">
                                <span>快照池</span>
                              </a-col>
                              <a-col :sm="6" :xs="24">
                                <a-input-number
                                      :defaultValue="100"
                                      :min="0"
                                      :max="100000"
                                      :formatter="value => `${value}GB`"
                                      :parser="value => value.replace('GB', '')"
                                      @change="onChange"
                                    />
                              </a-col>  
                              <a-col :sm="5" :xs="24" />
                            </a-row>
                          </a-form-item>
                          <a-alert message="选择无是不显示" banner closable />
                        </a-form>  
                        <a-alert message="仅列出容量大于等于目标磁盘/分区总容量110%,且未被设置镜像的磁盘/分区" banner closable />
                        <a-table :columns="mirr_columns" :dataSource="mirr_data" class="components-table-demo-nested">
                          <span slot="action" slot-scope="text, record">
                            <template slot="title">
                                <p>是否确定<b style="color:red">解除</b>镜像关系？</p>
                                <p>并同时<b style="color:red">删除</b>相关保护任务？</p>
                                <p>同步<b style="color:red">清除</b>历史数据</p>
                                <a-checkbox>解除镜像</a-checkbox>
                              </template>
                              <a>设置镜像</a>
                            </a-popconfirm>
                          </span>
                        </a-table> 
                      </a-modal>
                     </span>
                   </span>
                     <a-table
                       slot="expandedRowRender"
                       slot-scope="text"
                       :columns="innerColumns"
                       :dataSource="innerData"
                       :pagination="false"
                     >
                       <span slot="action" slot-scope="text" class="table-operation">
                         <a @click="() => set_Mirror(true)"><span>设置镜像</span></a>
                       </span>
                     </a-table>
                   </a-table>
               </a-modal>
             </a-menu-item>
             <a-menu-item key="3">
               <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
              <template slot="title">
                <p>是否确定删除该磁带库？</p>
                <a-checkbox>确认删除</a-checkbox>
              </template>
              <span>删除</span>
            </a-popconfirm></a>
             </a-menu-item>
           </a-menu>
         </a-dropdown>
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
    text: '正在保护'
  },
  1: {
    status: 'error',
    text: '故障'
  },
  2: {
    status: 'processing',
    text: '链接已断开'
  },
  3: {
    status: 'default',
    text: '未启动保护'
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
      add_host: false,
      selectedRowKeys: [],
      selectedRows: [],
      openKeys: [],
      selectedKeys: [],
      mirror_manage: false,
      mockData: [],
      targetKeys: [],
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      mirr_columns:[
        {
          title: '磁盘组',
          dataIndex: 'diskGroup',
        },
        {
          title: '磁盘名称',
          dataIndex: 'diskName',
        },
        {
          title: '容量',
          dataIndex: 'space',
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      mirr_data: [
        {
          diskGroup: 'jt',
          diskName: 'SYSVOL_DISK_jt_jt1',
          space: '12.0GB'
        },
        {
          diskGroup: 'jt',
          diskName: 'SYSVOL_DISK_jt_jt2',
          space: '80.0GB'
        }
      ],
      disk_columns: [
        {
          title: '磁盘编号',
          dataIndex: 'disknum',
        },
        {
          title: '容量',
          dataIndex: 'space',
        },
        {
          title: '分区数',
          dataIndex: 'partnum',
        },
        {
          title: '镜像磁盘',
          dataIndex: 'mirrordisk',
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      disk_data: [
        {
          disknum: '磁盘0',
          space: '80.0GB',
          partnum: '3',
          mirrordisk: '磁盘2*'
        },
        {
          disknum: '磁盘1',
          space: '40.0GB',
          partnum: '3',
          mirrordisk: ' '
        },
        {
          disknum: '磁盘2*',
          space: '80.0GB',
          partnum: '3',
          mirrordisk: '-'
        },
        {
          disknum: '磁盘3*',
          space: '80.0GB',
          partnum: '3',
          mirrordisk: ' '
        }
      ],
      innerColumns: [
        {
          title: '分区编号',
          dataIndex: 'partnum',
        },
        {
          title: '容量',
          dataIndex: 'space',
        },
        {
          title: '镜像分区',
          dataIndex: 'mirrordisk',
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      innerData: [
        {
          partnum: '分区1',
          space: '20.0GB',
          mirrordisk: ' ',
        },{
          partnum: '分区2',
          space: '20.0GB',
          mirrordisk: ' ',
        },{
          partnum: '分区3',
          space: '40.0GB',
          mirrordisk: ' ',
        },
      ],
      columns: [
         {
           title: '主机名称',
           dataIndex: 'hostname',
         },
         {
           title: '主机IP',
           dataIndex: 'hostip'
         },
         {
           title: '端口',
           dataIndex: 'port'
         },
         {
           title: '系统类别',
           dataIndex: 'ostype'
         },
         {
           title: '保护范围',
           dataIndex: 'portect',
           scopedSlots: { customRender: 'portect' }
         },
         {
           title: '状态',
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
       data: [
         {
           key: '0',
           hostname: 'Win-1',
           hostip: '192.168.0.15',
           port: '1100',
           ostype: 'Windows',
           portect: ['Disk1','Disk2-F盘'],
           status: '0'
         },
         {
           key: '1',
           hostname: 'Linux-1',
           hostip: '192.168.0.19',
           port: '1100',
           ostype: 'Linux',
           portect: ['sda','sdb1'],
           status: '2'
         },
         {
           key: '2',
           hostname: 'Linux-2',
           hostip: '192.168.0.29',
           port: '1100',
           ostype: 'Linux',
           status: '3'
         },
         {
           key: '3',
           hostname: 'Linux-3',
           hostip: '192.168.1.19',
           port: '1100',
           ostype: 'Linux',
           status: '1'
         },
       ],
       add_Host(add_host){
         this.add_host = add_host;
       },
       mirror_Manage(mirror_manage){
         this.mirror_manage = mirror_manage;
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
  mounted() {
        this.getMock();
      },
  methods: {
    addTask () {
      this.$router.push('/cdpset/addTask/addTask')
    },
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
