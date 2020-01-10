<template>
  <a-card :bordered="false">
    <div class="table-operator" style="margin-bottom: 10px;">
      <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0" >
        <a-menu slot="overlay">
          <a-menu-item key="1">
            <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
              <template slot="title">
                <p>是否确定批量删除所有已选节点？</p>
                <a-checkbox>确认修改</a-checkbox>
              </template>
              <a-icon type="delete"/> <a>批量删除</a>
            </a-popconfirm>
          </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作 <a-icon type="down" />
        </a-button>
      </a-dropdown>
    
    </div>
    <div style="margin-bottom: 10px;">
      <a-alert message="取消共享协议显示,当CIFS或NFS有任何一个共享开启式则显示该设备在线,均关闭时显示离线" banner closable />
      <a-alert message="手动强制离线时同时关闭CIFS和NFS共享,但须保留原有配置状态,手动上显示恢复离线钱状态,即仍需要有独立参数控制是否离线" banner closable />
    </div>
   <template>
     <a-table :columns="columns" :dataSource="data" class="components-table-demo-nested">
       <span slot="snapshot" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
       <span slot="status" slot-scope="text">
         <a-badge :status="text | useTypeFilter" :text="text | useFilter" />
       </span>
       <span slot="cifs" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
       <span slot="nfs" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
       <span slot="action" slot-scope="text, record">
         <a-dropdown :trigger="['click']">
             <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
             <a-menu slot="overlay">
              <a-menu-item key="10">
                <a @click="() => modify_Cifs(true)">CIFS设置</a>
                <a-modal
                      title="XXX共享CIFS设置"
                      centered
                      v-model="modify_cifs"
                      @ok="() => modify_cifs = false"
                      :width="400"
                      :visible="visible"
                   >
                   <a-form :form="form">
                     <a-form-item>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                          <a-switch checkedChildren="开" unCheckedChildren="关" defaultChecked />
                         </a-col>
                         <a-col :sm="13" :xs="24">
                          <span>开启 CIFS 共享</span>
                         </a-col>
                       </a-row >
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                           <span>共享名称：</span>
                         </a-col>
                         <a-col :sm="13" :xs="24">
                           <a-input />
                         </a-col>  
                       </a-row>
                     </a-form-item>
                   </a-form>   
                </a-modal>
              </a-menu-item>
              <a-menu-item key="11">
                <a @click="() => modify_Nfs(true)">NFS设置</a>
                <a-modal
                      title="XXX共享NFS设置"
                      centered
                      v-model="modify_nfs"
                      @ok="() => modify_nfs = false"
                      :width="400"
                      :visible="visible"
                   >
                   <a-form :form="form">
                     <a-form-item>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                          <a-switch checkedChildren="开" unCheckedChildren="关" defaultChecked />
                         </a-col>
                         <a-col :sm="13" :xs="24">
                          <span>开启 NFS 共享</span>
                         </a-col>
                       </a-row >
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                          <a-switch checkedChildren="开" unCheckedChildren="关" defaultChecked />
                         </a-col>
                         <a-col :sm="13" :xs="24">
                          <span>开启匿名访问</span>
                         </a-col>
                       </a-row >
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                          <a-switch checkedChildren="开" unCheckedChildren="关" defaultChecked />
                         </a-col>
                         <a-col :sm="13" :xs="24">
                          <span>开启匿名读写</span>
                         </a-col>
                       </a-row >
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                           <span>读写权限：</span>
                         </a-col>
                         <a-col :sm="10" :xs="24">
                           <a-input />
                         </a-col>  
                         <a-col :sm="3" :xs="24">
                           <a-button type="primary" icon="plus">添加</a-button>
                         </a-col>  
                       </a-row>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24"/>
                         <a-col :sm="13" :xs="24">
                           <a-list size="small" :dataSource="rwdata">
                             <a-list-item slot="renderItem" slot-scope="item, index">
                               <div style="float: left;">
                                 <div style="float: left;width:150px">
                                  {{item}}
                                 </div>
                                 <div style="float: right; width:30px;">
                                   <a><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                     <template slot="title">
                                       <p>是否确定<b style="color:red">删除</b>该目标？</p>
                                       <a-checkbox>确认删除</a-checkbox>
                                     </template>
                                     <a>删除</a>
                                   </a-popconfirm></a>
                                 </div>
                               </div>
                             </a-list-item>
                           </a-list>
                         </a-col>  
                       </a-row>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                           <span>只读权限：</span>
                         </a-col>
                         <a-col :sm="10" :xs="24">
                           <a-input />
                         </a-col>  
                         <a-col :sm="3" :xs="24">
                           <a-button type="primary" icon="plus">添加</a-button>
                         </a-col>  
                       </a-row>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24"/>
                         <a-col :sm="13" :xs="24">
                           <a-list size="small" :dataSource="rwdata">
                             <a-list-item slot="renderItem" slot-scope="item, index">
                               <div style="float: left;">
                                 <div style="float: left;width:150px">
                                  {{item}}
                                 </div>
                                 <div style="float: right; width:30px;">
                                   <a><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                     <template slot="title">
                                       <p>是否确定<b style="color:red">删除</b>该目标？</p>
                                       <a-checkbox>确认删除</a-checkbox>
                                     </template>
                                     <a>删除</a>
                                   </a-popconfirm></a>
                                 </div>
                               </div>
                             </a-list-item>
                           </a-list>
                         </a-col>  
                       </a-row>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                           <span>ROOT权限：</span>
                         </a-col>
                         <a-col :sm="10" :xs="24">
                           <a-input />
                         </a-col>  
                         <a-col :sm="3" :xs="24">
                           <a-button type="primary" icon="plus">添加</a-button>
                         </a-col>  
                       </a-row>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24"/>
                         <a-col :sm="13" :xs="24">
                           <a-list size="small" :dataSource="rwdata">
                             <a-list-item slot="renderItem" slot-scope="item, index">
                               <div style="float: left;">
                                 <div style="float: left;width:150px">
                                  {{item}}
                                 </div>
                                 <div style="float: right; width:30px;">
                                   <a><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                                     <template slot="title">
                                       <p>是否确定<b style="color:red">删除</b>该目标？</p>
                                       <a-checkbox>确认删除</a-checkbox>
                                     </template>
                                     <a>删除</a>
                                   </a-popconfirm></a>
                                 </div>
                               </div>
                             </a-list-item>
                           </a-list>
                         </a-col>  
                       </a-row>
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                          <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />
                         </a-col>
                         <a-col :sm="13" :xs="24">
                          <span>开启高级设置</span>
                         </a-col>
                       </a-row >
                       <a-row :gutter="20">
                         <a-col :sm="7" :xs="24">
                         </a-col>
                         <a-col :sm="13" :xs="24">
                          <a-textarea placeholder="Basic usage" :rows="4" />
                         </a-col>
                       </a-row >
                     </a-form-item>
                   </a-form>   
                </a-modal>
              </a-menu-item>
               <a-menu-item key="0">
                 <span v-if="record.status === '0'">
                   <span><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                     <template slot="title">
                       <p>是否确定<b style="color:red">离线</b>该共享目录？</p>
                       <a-checkbox>确认离线</a-checkbox>
                     </template>
                     <span>离线</span>
                   </a-popconfirm></span>
                 </span>
                 <span v-else>
                   <a><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                     <template slot="title">
                       <p>是否确定<b style="color:red">上线</b>该共享目录？</p>
                       <a-checkbox>确认上线</a-checkbox>
                     </template>
                     <span>上线</span>
                   </a-popconfirm></a>
                 </span>
               </a-menu-item>
               <a-sub-menu title="快照保护" key="1">
                  <a-menu-item>
                    <a @click="() => list_Snapshot(true)">快照清单</a>
                    <a-modal
                          title="快照清单"
                          centered
                          v-model="list_snapshot"
                          @ok="() => list_snapshot = false"
                          :width="700"
                          :visible="visible"
                          :confirmLoading="confirmLoading"
                       >
                         
                       <a-spin :spinning="confirmLoading">
                         <a-form :form="form">
                           <a-form-item>
                             <a-row :gutter="48">
                               <a-col :sm="4" :xs="24">
                                 <a-dropdown>
                                   <a-menu slot="overlay">
                                     <a-menu-item key="1">
                                       <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                         <template slot="title">
                                           <p>是否确定<b style="color:red">批量删除</b>所有已选快照？</p>
                                           <a-checkbox>确认删除</a-checkbox>
                                         </template>
                                         <a-icon type="delete"/> <a>批量删除</a>
                                       </a-popconfirm>
                                     </a-menu-item>
                                   </a-menu>
                                   <a-button style="margin-left: 8px">
                                     批量操作 <a-icon type="down" />
                                   </a-button>
                                 </a-dropdown>
                               </a-col>
                               <a-col :sm="6" :xs="24">
                                 <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                   <template slot="title">
                                     <p>是否确定<b style="color:red">手动创建快照？</b></p>
                                     <a-checkbox>确认创建</a-checkbox>
                                   </template>
                                   <a-button>创建快照</a-button>
                                 </a-popconfirm>
                               </a-col>
                             </a-row>
                           </a-form-item>
                             <a-table :columns="sp_columns" :dataSource="sp_data" :rowSelection="rowSelection">
                               <span slot="action" slot-scope="text, record">
                                 <a-dropdown :trigger="['click']">
                                   <a class="ant-dropdown-link" > 操作 <a-icon type="down" /> </a>
                                   <a-menu slot="overlay">
                                     <a-menu-item key="0">
                                       <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                         <template slot="title">
                                           <p>是否确定<b style="color:red">回滚</b>选中快照？</p>
                                           <a-checkbox>确认回滚</a-checkbox>
                                         </template>
                                         <span>回滚快照</span>
                                       </a-popconfirm>
                                     </a-menu-item>
                                     <a-menu-item key="0">
                                       <a-popconfirm placement="top" okText="Yes" cancelText="No">
                                         <template slot="title">
                                           <p>是否确定<b style="color:red">删除</b>选中快照？</p>
                                           <a-checkbox>确认删除</a-checkbox>
                                         </template>
                                         <span>删除快照</span>
                                       </a-popconfirm>
                                     </a-menu-item>
                                   </a-menu>
                                 </a-dropdown>
                               </span>
                             </a-table>
                           </a-form-item>
                         </a-form>   
                       </a-spin>
                    </a-modal>
                  </a-menu-item>
                  <a-menu-item>
                    <a @click="() => auto_Snapshot(true)">自动快照</a>
                    <a-modal
                          title="设置XXX存储空间自动快照"
                          centered
                          v-model="auto_snapshot"
                          @ok="() => auto_snapshot = false"
                          :width="400"
                          :visible="visible"
                          :confirmLoading="confirmLoading"
                       >
                       <a-spin :spinning="confirmLoading">
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
                         </a-form>   
                       </a-spin>
                    </a-modal>
                  </a-menu-item>
                </a-sub-menu>
               <a-menu-item key="2">
                 <a @click="() => por_Config(true)"><span>属性设置</span></a>
                 <a-modal
                       title="共享属性设置"
                       centered
                       v-model="por_config"
                       @ok="() => por_config = false"
                       :width="700"
                       :visible="visible"
                       :confirmLoading="confirmLoading"
                    >
                    <a-spin :spinning="confirmLoading">
                      <a-form-item>
                          <a-row :gutter="20">
                            <a-col :sm="7" :xs="24">
                              <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用重复数据删除
                            </a-col>
                            <a-col :sm="7" :xs="24">
                              <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用数据校验
                            </a-col>
                          </a-row>
                        </a-form-item>
                        <a-form-item>
                          <a-row :gutter="20">
                            <a-col :sm="7" :xs="24">
                              <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用数据压缩
                            </a-col>
                            <a-col :sm="7" :xs="24">
                              <a-select defaultValue="高效" style="width: 120px" @change="handleChange">
                                    <a-select-option value="中等">中等</a-select-option>
                                    <a-select-option value="高等">高等</a-select-option>
                                    <a-select-option value="高效">高效</a-select-option>
                                  </a-select>
                            </a-col>
                          </a-row>
                        </a-form-item>
                        <a-form-item>
                          <a-row :gutter="20">
                            <a-col :sm="7" :xs="24">
                              <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用配额管理
                            </a-col>
                            <!-- <a-col :sm="7" :xs="24">
                              <span>最大分配空间</span>
                            </a-col> -->
                            <a-col :sm="5" :xs="24">
                              <a-input-number
                                :defaultValue="100"
                                :min="0"
                                :max="100000"
                                :formatter="value => `${value}GB`"
                                :parser="value => value.replace('GB', '')"
                              />
                              </div>
                            </a-col>  
                            <a-col :sm="8" :xs="24">
                              <a-popover placement="right">
                                <template slot="content">
                                  <span>不得小于4GB</span>
                                </template>
                                <a-button shape="circle" icon="question-circle" :size="small"/>
                              </a-popover>
                             </a-col>
                          </a-row>
                        </a-form-item>
                        <a-form-item>
                          <a-row :gutter="20">
                            <a-col :sm="7" :xs="24">
                              <span>块大小</span>
                            </a-col>
                            <a-col :sm="13" :xs="24">
                              <a-select defaultValue="128KB" style="width: 120px" @change="handleChange">
                                <a-select-option value="4">4KB</a-select-option>
                                <a-select-option value="8">8KB</a-select-option>
                                <a-select-option value="16">16KB</a-select-option>
                                <a-select-option value="32">32KB</a-select-option>
                                <a-select-option value="64">64KB</a-select-option>
                                <a-select-option value="128">128KB</a-select-option>
                              </a-select>
                            </a-col>
                          </a-row>
                        </a-form-item>
                        <a-form-item>
                          <a-row :gutter="20">
                            <a-col :sm="7" :xs="24">
                              <span>同步写入</span>
                            </a-col>
                            <a-col :sm="13" :xs="24">
                              <a-select defaultValue="标准" style="width: 120px" @change="handleChange">
                                <a-select-option value="总是">总是</a-select-option>
                                <a-select-option value="标准">标准</a-select-option>
                                <a-select-option value="关闭">关闭</a-select-option>
                              </a-select>
                            </a-col>
                          </a-row>
                        </a-form-item>
                        <a-form-item>
                          <a-row :gutter="20">
                            <a-col :sm="7" :xs="24">
                              <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />授权用户
                            </a-col>
                            <a-col :sm="13" :xs="24">
                              <a-select  style="width: 120px" @change="handleChange" />
                            </a-col>
                          </a-row>
                        </a-form-item>
                      </a-form>   
                    </a-spin>
                 </a-modal>
               </a-menu-item>
               <a-menu-item key="5">
                  <a @click="() => show_Pro(true)"><span>共享详情</span></a>
               </a-menu-item>
               <a-menu-item key="6">
                  <a @click="() => modify_Perm(true)"><span>修改权限</span></a>
               </a-menu-item>
               <a-menu-item key="7">
                 <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                <template slot="title">
                  <p>是否确定<b style="color:red">删除</b>该共享？</p>
                  <a-checkbox>确认删除</a-checkbox>
                </template>
                <span>删除</span>
              </a-popconfirm>
               </a-menu-item>
             </a-menu>
           </a-dropdown>
           <a-modal
               title="XXX共享详细信息"
               centered
               v-model="show_pro"
               @ok="() => show_pro = false"
               :width="700"
               :visible="visible"
             >
            <a-form :form="form">
              <a-form-item>
                <a-alert message="应该根据共享的协议显示以下内容" banner />
              </a-form-item>
              <a-form-item>
                NFS 参数
              </a-form-item>
              <a-form-item>
                <a-table :columns="nfs_columns" :dataSource="nfs_data" :pagination="false" class="components-table-demo-nested" />
              </a-form-item>
              <a-form-item>
                CIFS 参数
              </a-form-item>
              <a-form-item>
                <a-table :columns="cifs_columns" :dataSource="cifs_data" :pagination="false" class="components-table-demo-nested" />
              </a-form-item>
            </a-form>
          </a-modal>
          
           <a-modal
               title="XXX共享权限"
               centered
               v-model="modify_perm"
               @ok="() => modify_perm = false"
               :width="700"
               :visible="visible"
             >
            <a-table :columns="share_columns" :dataSource="share_data" class="components-table-demo-nested" > 
              <span slot="permission" slot-scope="permission">
                <a-tag
                  v-for="tag in permission"
                  :color="tag==='loser' ? 'volcano' : (tag.length > 5 ? 'geekblue' : 'green')"
                  :key="tag"
                >
                  {{tag.toUpperCase()}}
                </a-tag>
              </span>
              <span slot="action" slot-scope="text, record">
                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="confirm">
                  <template slot="title">
                    <p>是否确定<b style="color:red">删除</b>该目录的共享权限？</p>
                    <a-checkbox>确认删除</a-checkbox>
                  </template>
                  <a>删除</a>
                </a-popconfirm>
              </span>
            </a-table>
            <a-divider>添加权限</a-divider>
            <a-alert message="目前两个多选框有联动,需要修改" banner />
            <a-alert message="对于已设置过的目录,权限直接更新" banner />
            <a-form :form="form">
              <a-form-item>
                <a-row :gutter="20">
                  <a-col :sm="9" :xs="24">
                    <a-tree-select
                      showSearch
                      :value="value"
                      :dropdownStyle="{ maxHeight: '400px', overflow: 'auto' }"
                      placeholder="Please select"
                      allowClear
                      multiple
                      treeDefaultExpandAll
                      @change="onChange"
                      @search="onSearch"
                      @select="onSelect"
                    >
                      <a-tree-select-node value="parent 1" title="parent 1" key="0-1">
                        <a-tree-select-node value="parent 1-0" title="parent 1-0" key="0-1-1">
                          <a-tree-select-node value="leaf1" title="my leaf" key="random" />
                          <a-tree-select-node value="leaf2" title="your leaf" key="random1" />
                        </a-tree-select-node>
                        <a-tree-select-node value="parent 1-1" title="parent 1-1" key="random2">
                          <a-tree-select-node value="sss" key="random3">
                            <b style="color: #08c" slot="title">sss</b>
                          </a-tree-select-node>
                        </a-tree-select-node>
                      </a-tree-select-node>
                    </a-tree-select>
                  </a-col>
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
                  <a-col :sm="2" :xs="24">
                    <a-button type="primary" icon="plus">添加</a-button>
                  </a-col>
                </a-row>
              </a-form-item>
            </a-form>
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
import { TreeSelect } from 'ant-design-vue';
const SHOW_PARENT = TreeSelect.SHOW_PARENT;
const statusMap = {
  0: {
    status: 'success',
    text: '已开启'
  },
  1: {
    status: 'default',
    text: '已关闭'
  },
}
const useMap = {
  
  0: {
    status: 'success',
    text: '在线'
  },
  1: {
    status: 'default',
    text: '离线'
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
      
      value: ['0-0-0'],
      SHOW_PARENT,
      visible: false,
      confirmLoading: false,
      form: this.$form.createForm(this),
      list_snapshot: false,
      auto_snapshot: false,
      show_pro: false,
      tapes: false,
      por_config: false,
      modify_cifs: false,
      modify_nfs: false,
      modify_perm: false,
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
      rwdata: [
          '192.168.0.20',
          '192.168.10.20',
          '192.168.20.20',
          '192.168.30.20',
          '192.168.40.20',
        ],
      permissions: [
        {
          title: '测试组',
          value: '0-0',
          key: '0-0',
          children: [
            {
              title: '1号组',
              value: '0-0-0',
              key: '0-0-0',
              children: [
                {
                  title: '组员1',
                  value: '0-0-0-0',
                  key: '0-0-0-0',
                },
                {
                  title: '组员2',
                  value: '0-0-0-1',
                  key: '0-0-0-1',
                },
              ],
            },
          ],
        },
        {
          title: '研发组',
          value: '0-1',
          key: '0-1',
          children: [
            {
              title: '1号组',
              value: '0-1-0',
              key: '0-1-0',
              children: [
                {
                  title: '组员1',
                  value: '0-1-0-0',
                  key: '0-1-0-0',
                },
                {
                  title: '组员2',
                  value: '0-1-0-1',
                  key: '0-1-0-1',
                },
              ],
            },
          ],
        },
        {
          title: '用户组',
          value: '0-2',
          disabled: true,
          key: '0-2',
          children: [
            {
              title: '1号组',
              value: '0-2-0',
              key: '0-2-0',
              children: [
                {
                  title: '组员1',
                  value: '0-2-0-0',
                  key: '0-2-0-0',
                },
                {
                  title: '组员2',
                  value: '0-2-0-1',
                  key: '0-2-0-1',
                },
              ],
            },
          ],
        },
      ],
      share_columns: [
        {
          title: '目录',
          dataIndex: 'contents',
        },
        {
          title: '权限',
          dataIndex: 'permission',
          scopedSlots: { customRender: 'permission' }
        },
         {
           title: '操作',
           dataIndex: 'action',
           width: '100px',
           scopedSlots: { customRender: 'action' }
         }
      ],
      share_data: [
        {
          key: '0',
          contents: 'SYSBVOL/NAS/ttttt',
          permission: ['系统ACL', '测试组'],
        }
      ],
      nfs_columns: [
        {
          title: '类别',
          dataIndex: 'type',
        },
        {
          title: '参数',
          dataIndex: 'value',
        },
      ],
      nfs_data: [
        {
          key: '0',
          type: '匿名访问',
          value: '禁止'
        },
        {
          key: '1',
          type: '匿名读写',
          value: '禁止'
        },
        {
          key: '2',
          type: '读写权限',
          value: ' '
        },
        {
          key: '3',
          type: '只读权限',
          value: ' '
        },
        {
          key: '4',
          type: 'ROOT权限',
          value: ' '
        },
      ],
      cifs_columns: [
        {
          title: '类别',
          dataIndex: 'type',
        },
        {
          title: '参数',
          dataIndex: 'value',
        },
      ],
      cifs_data: [
        {
          key: '0',
          type: '共享名称',
          value: ' '
        },
      ],
      columns: [
         {
           title: '共享目录',
           dataIndex: 'sharepath',
         },
         {
           title: '状态',
           dataIndex: 'status',
           scopedSlots: { customRender: 'status' }
         },
         {
           title: '总大小',
           dataIndex: 'space'
         },
         {
           title: '剩余空间',
           dataIndex: 'freespace'
         },
         {
           title: '自动快照',
           dataIndex: 'snapshot',
           scopedSlots: { customRender: 'snapshot' }
         },
         {
           title: 'CIFS功能',
           dataIndex: 'cifs',
           scopedSlots: { customRender: 'cifs' }
         },
         {
           title: 'NFS功能',
           dataIndex: 'nfs',
           scopedSlots: { customRender: 'nfs' }
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
           status: '0',
           sharepath: '/SYSVOL/NAS/tttttt',
           space: '90GB',
           snapshot: '0',
           freespace: '89%',
           cifs: '0',
           nfs: '1',
         }
       ],
       list_Snapshot(list_snapshot){
         this.list_snapshot = list_snapshot;
       },
       auto_Snapshot(auto_snapshot){
         this.auto_snapshot = auto_snapshot;
       },
       modify_Cifs(modify_cifs){
         this.modify_cifs = modify_cifs;
       },
       modify_Nfs(modify_nfs){
         this.modify_nfs = modify_nfs;
       },
       por_Config(por_config){
         this.por_config = por_config;
       },
      show_Pro(show_pro){
        this.show_pro = show_pro},
      modify_Perm(modify_perm){
        this.modify_perm = modify_perm},
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
   useFilter (type) {
     return useMap[type].text
   },
   useTypeFilter (type) {
     return useMap[type].status
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
    onChange(value) {
      console.log('onChange ', value);
      this.value = value;
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
