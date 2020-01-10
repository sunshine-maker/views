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
            <a-form-item label="保护类别">
              <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                <a-select-option value="0">快照级</a-select-option>
                <a-select-option value="1">记录级</a-select-option>
                <a-select-option value="2">无</a-select-option>
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
    <div class="table-operator" style="margin:0 0 10px -5px;">
      <a-dropdown v-action:edit v-if="hasSelected">
        <a-menu slot="overlay">
          <a-menu-item key="1"><a-icon type="delete" />批量删除</a-menu-item>
          <!-- lock | unlock -->
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作 <a-icon type="down" />
        </a-button>
      </a-dropdown>
    </div>
    <div style="margin-bottom: 10px;">
    <a-alert message="********" banner closable />
    </div>
   <template>
     <a-table :columns="columns" :dataSource="data" :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}" class="components-table-demo-nested">
       <span slot="status" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
       <span slot="action" slot-scope="text, record">
         <a-dropdown :trigger="['click']">
           <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
           <a-menu slot="overlay">
             <a-menu-item key="5">
               <a @click="() => show_Info(true)"><span>详细信息</span></a>
               <a-modal
                   title="XXX任务运行详细信息"
                   centered
                   v-model="show_info"
                   @ok="() => show_info = false"
                   :width="600"
                   :visible="visible"
                 >
                <a-alert message="初始同步完成后,保护状态项仅显示'正在保护'" banner closable />
                 <a-table :columns="info_columns" :dataSource="info_data"class="components-table-demo-nested" :pagination="false"/>
                </a-modal>
             </a-menu-item>
             <a-menu-item key="0">
               <span v-if="record.protect==='无'">
                 <a disabled="True"><span>快照清单</span></a>
               </span>
               <span v-else>
                 <span v-if="record.snapshotime ==='-'">
                   <a disabled="True"><span>快照清单</span></a>
                 </span>
                 <span v-else>
                  <a @click="() => snapshot_List(true)"><span>快照清单</span></a>
                  <a-modal
                      title="快照清单"
                      centered
                      v-model="snapshot_list"
                      @ok="() => snapshot_list = false"
                      :width="700"
                      :visible="visible"
                    >
                    <a-alert message="记录级快照菜单中有'详细记录'" banner closable />
                    <div class="table-page-search-wrapper">
                      <a-form layout="inline">
                        <a-row :gutter="48">
                          <a-col :md="8" :sm="24">
                            <a-date-picker
                              :disabledDate="disabledStartDate"
                              showTime
                              format="YYYY-MM-DD HH:mm:ss"
                              v-model="startValue"
                              placeholder="起始时间"
                              @openChange="handleStartOpenChange"
                            />
                          </a-col>
                          <a-col :md="8" :sm="24">
                             <a-date-picker
                               :disabledDate="disabledEndDate"
                               showTime
                               format="YYYY-MM-DD HH:mm:ss"
                               placeholder="结束时间"
                               v-model="endValue"
                               :open="endOpen"
                               @openChange="handleEndOpenChange"
                             />
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
                    <div class="table-operator" style="margin:0 0 10px 0;">
                      <a-row >
                        <a-col :md="5" :sm="24">
                          <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                            <template slot="title">
                              <p>是否确定<b style="color:red">创建</b>快照？</p>
                              <a-checkbox>确认创建</a-checkbox>
                            </template>
                            <a-button type="primary">
                              <a-icon type="plus" />创建快照 
                            </a-button>
                          </a-popconfirm>
                        </a-col>
                        <a-col :md="5" :sm="24">
                          <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                            <template slot="title">
                              <p>是否确定<b style="color:red">校准</b>快照？</p>
                              <a-checkbox>确认校准</a-checkbox>
                            </template>
                            <a-button type="primary">
                              <a-icon type="compass" />校准快照 
                            </a-button>
                          </a-popconfirm>
                        </a-col>
                        <a-col :md="5" :sm="24">
                        <a-dropdown v-action:edit v-if="hasSelected">
                          <a-menu slot="overlay">
                            <a-menu-item key="1"><a-icon type="delete" />批量删除</a-menu-item>
                            <!-- lock | unlock -->
                          </a-menu>
                          <a-button style="margin-left: 8px">
                            批量操作 <a-icon type="down" />
                          </a-button>
                        </a-dropdown>
                      </a-col>
                      <a-col :md="1" :sm="24">
                        <a-popover placement="right">
                          <template slot="content">
                            <p>磁盘组文件系统快照可能出现与数据库快照不一致的情况时</p>
                            <p>点 击“【快照校准】”，可以自动删除数据库中不存在的后台文件系统快照，释放空间。</p>
                          </template>
                          <a-icon type="question-circle" />
                        </a-popover>
                      </a-col>
                    </a-row>
                    </div>
                     <a-table :columns="log_columns" :dataSource="log_data" :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}" class="components-table-demo-nested">
                      <span slot="action" slot-scope="text, record">
                        <a-dropdown :trigger="['click']">
                          <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
                          <a-menu slot="overlay">
                            <a-menu-item key="0">
                              <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                <template slot="title">
                                  <p>是否确定<b style="color:red">创建</b>该快照副本？</p>
                                  <a-checkbox>确认创建</a-checkbox>
                                </template>
                                <span>创建副本</span>
                              </a-popconfirm>
                            </a-menu-item>
                            <a-menu-item key="1">
                              <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                <template slot="title">
                                  <p>是否确定<b style="color:red">暂停</b>该保护任务，<b style="color:red">中断</b>Lun映射，并<b style="color:red">回滚</b>该快照？</p>
                                  <a-checkbox>确认回滚</a-checkbox>
                                </template>
                                <span>回滚快照</span>
                              </a-popconfirm>
                            </a-menu-item>
                            <a-menu-item key="2">
                              <a @click="() => show_Log(true)"><span>详细记录</span></a>
                              <a-modal
                                  title="XXXX任务详细记录"
                                  centered
                                  v-model="show_log"
                                  @ok="() => show_log = false"
                                  :width="500"
                                  :visible="visible"
                                >
                                <div class="table-operator" style="margin:0 0 10px 0;">
                                  <a-dropdown v-action:edit v-if="hasSelected">
                                    <a-menu slot="overlay">
                                      <a-menu-item key="1"><a-icon type="delete" />批量删除</a-menu-item>
                                      <!-- lock | unlock -->
                                    </a-menu>
                                    <a-button style="margin-left: 8px">
                                      批量操作 <a-icon type="down" />
                                    </a-button>
                                  </a-dropdown>
                                </div>
                                <a-table :columns="io_columns" :dataSource="io_data" :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}" class="components-table-demo-nested">
                                  <span slot="action" slot-scope="text, record">
                                    <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                      <template slot="title">
                                        <p>是否确定<b style="color:red">暂停</b>该保护任务，<b style="color:red">中断</b>Lun映射，并<b style="color:red">回滚</b>该快照？</p>
                                        <a-checkbox>确认回滚</a-checkbox>
                                      </template>
                                      <a>回滚快照</a>
                                    </a-popconfirm>
                                  </span>
                                </a-table>
                              </a-modal>
                            </a-menu-item>
                            <a-menu-item key="3">
                              <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                <template slot="title">
                                  <p>是否确定<b style="color:red">删除</b>该任务？</p>
                                  <a-checkbox>确认删除</a-checkbox>
                                </template>
                                <span>删除任务</span>
                              </a-popconfirm>
                            </a-menu-item>
                          </a-menu>
                        </a-dropdown>
                      </span>
                     </a-table>
                    </a-modal>
                 </span>
               </span>
             </a-menu-item>
             <a-menu-item key="1">
                <a @click="() => modify_Task(true)"><span>修改策略</span></a>
                <a-modal
                    title="修改策略"
                    centered
                    v-model="modify_task"
                    @ok="() => modify_task = false"
                    :width="500"
                    :visible="visible"
                  >
                   <a-form :form="form">
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
                 </a-modal>
             </a-menu-item>
             <a-menu-item key="2">
               <span v-if="record.status <'3'">
                 <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                   <template slot="title">
                     <p>是否确定<b style="color:red">暂停</b>该任务？</p>
                     <a-checkbox>暂停保护</a-checkbox>
                   </template>
                   <span>暂停保护</span>
                 </a-popconfirm>
               </span>
               <span v-else-if="record.status ==='3'"> 
                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                  <template slot="title">
                    <p>是否确定<b style="color:red">取消</b>回滚操作？</p>
                    <a-checkbox>取消回滚</a-checkbox>
                  </template>
                  <span>取消回滚</span>
                </a-popconfirm>
               </span>
               <span v-else-if="record.status ==='4'">
                 <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                   <template slot="title">
                     <p>是否确定<b style="color:red">启动</b>该任务？</p>
                     <a-checkbox>启动保护</a-checkbox>
                   </template>
                   <span>启动保护</span>
                 </a-popconfirm>
               </span>
             </a-menu-item>
            <a-menu-item key="3">
              <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                <template slot="title">
                  <p>是否确定<b style="color:red">取消</b>该任务的相关<b style="color:red">磁盘镜像</b>，并<b style="color:red">删除</b>该任务？</p>
                  <a-checkbox>确认删除</a-checkbox>
                </template>
                <span>删除任务</span>
              </a-popconfirm>
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
    status: 'success',
    text: '正在回滚'
  },
  4: {
    status: 'default',
    text: '已停止'
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
      modify_task: false,
      snapshot_list: false,
      show_log: false,
      show_info: false,
      selectedRowKeys: [],
      openKeys: [],
      selectedKeys: [],
      mockData: [],
      targetKeys: [],
      startValue: null,
      endValue: null,
      endOpen: false,
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      info_columns: [
        {
          title: '信息',
          dataIndex: 'info',
        },
        {
          title: '内容',
          dataIndex: 'data',
        }
      ],
      info_data: [
        {
          info: '主机名称',
          data: 'Win-1'
        },
        {
          info: '主机IP',
          data: '192.168.0.19'
        },
        {
          info: '保护内容',
          data: '磁盘0-分区1'
        },
        {
          info: '目标容量',
          data: '30.0GB'
        },
        {
          info: '异步保护',
          data: '启用'
        },
        {
          info: '保护状态',
          data: '初始同步 3%（1,107,890,444） 用时15分钟24秒'
        },
        {
          info: '保护开始时间',
          data: '2020-1-9 12:05:58'
        },
        {
          info: '保护停止时间',
          data: ' '
        },
      ],
      io_columns: [
        {
          title: '记录时间',
          dataIndex: 'iotime',
        },
        {
          title: '数据变化量',
          dataIndex: 'chagedata',
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      io_data: [
        {
          key: '0',
          iotime: '2020-1-9 09:18:27',
          chagedata: '18KB'
        },
        {
          key: '1',
          iotime: '2020-1-9 09:18:27',
          chagedata: '86KB'
        },
      ],
      log_columns: [
        {
          title: '代理',
          dataIndex: 'agent',
        },
        {
          title: '记录时间',
          dataIndex: 'logtime',
        },
        {
          title: '数据变化量',
          dataIndex: 'changedata',
        },
        {
          title: '操作',
          dataIndex: 'action',
          width: '100px',
          scopedSlots: { customRender: 'action' }
        }
      ],
      log_data: [
        {
          key : '0',
          agent: '默认代理',
          logtime: '2020-1-9 09:18:27',
          changedata: '198MB',
        },
        {
          key : '1',
          agent: '默认代理',
          logtime: '2020-1-9 09:18:27',
          changedata: '28MB',
        }
      ],
      columns: [
         {
           title: '任务名称',
           dataIndex: 'taskname',
         },
         {
           title: '主机IP',
           dataIndex: 'hostip'
         },
         {
           title: '保护级别',
           dataIndex: 'portect',
         },
         {
           title: '最近快照',
           dataIndex: 'snapshotime'
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
           taskname: 'Win-1',
           hostip: '192.168.0.15',
           portect: '快照级',
           snapshotime: '2020-1-9 09:18:27',
           status: '0'
         },
         {
           key: '1',
           taskname: 'Linux-1',
           hostip: '192.168.0.19',
           portect: '记录级',
           snapshotime: '2020-1-9 09:18:27',
           status: '2'
         },
         {
           key: '2',
           taskname: 'Linux-2',
           hostip: '192.168.0.29',
           portect: '无',
           snapshotime: '-',
           status: '3'
         },
         {
           key: '3',
           taskname: 'Linux-3',
           hostip: '192.168.0.29',
           portect: '无',
           snapshotime: '-',
           status: '4'
         },
       ],
       show_Info(show_info){
         this.show_info = show_info;
       },
       show_Log(show_log){
         this.show_log = show_log;
       },
       snapshot_List(snapshot_list){
         this.snapshot_list = snapshot_list;
       },
       modify_Task(modify_task){
         this.modify_task = modify_task;
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
    hasSelected() {
      return this.selectedRowKeys.length > 0;
    },
  },
  mounted() {
        this.getMock();
      },
  methods: {
    disabledStartDate(startValue) {
      const endValue = this.endValue;
      if (!startValue || !endValue) {
        return false;
      }
      return startValue.valueOf() > endValue.valueOf();
    },
    disabledEndDate(endValue) {
      const startValue = this.startValue;
      if (!endValue || !startValue) {
        return false;
      }
      return startValue.valueOf() >= endValue.valueOf();
    },
    handleStartOpenChange(open) {
      if (!open) {
        this.endOpen = true;
      }
    },
    handleEndOpenChange(open) {
      this.endOpen = open;
    },
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
    
    onSelectChange(selectedRowKeys) {
      console.log('selectedRowKeys changed: ', selectedRowKeys);
      this.selectedRowKeys = selectedRowKeys;
    },
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    }
  }
}
</script>
