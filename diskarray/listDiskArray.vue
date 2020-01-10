<template>
  <a-table :columns="columns" :dataSource="data">
       <span slot="status" slot-scope="text">
         <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
       </span>
    <span slot="address" slot-scope="address, record">
      <span v-if="record.address === '未映射'">
        <span style="color:#13C2C2">{{record.address}}</span>
      </span>
      <span v-else>
        <a-tag
          v-for="tag in address"
          :color="tag==='loser' ? 'volcano' : (tag.length > 5 ? 'geekblue' : 'green')"
          :key="tag"
        >
          {{tag.toUpperCase()}}
        </a-tag>
      </span>
    </span>
    
    <span slot="action" slot-scope="text, record">
      <a-dropdown :trigger="['click']">
          <a class="ant-dropdown-link" href="#"> 设置 <a-icon type="down" /> </a>
          <a-menu slot="overlay">
            <a-menu-item key="0">
              <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="() => add_Disk(true)">
             <template slot="title">
               <p>是否确定解除映射并添加磁盘？</p>
               <a-checkbox>确认修改</a-checkbox>
             </template>
             <a>添加磁盘</a>
           </a-popconfirm>
             <a-modal
                title="为磁盘组XXX添加磁盘"
                centered
                v-model="add_disk"
                @ok="() => add_disk = false"
                 :width="500"
                 :visible="visible"
               >
                  <a-form :form="form">
                    <a-form-item>
                      <a-row>
                        <a-col :sm="7" :xs="24">
                            <span>磁盘数量</span>
                        </a-col>
                        <a-col :sm="10" :xs="24">
                          <a-input-number size="large" :min="1" :max="100000" :defaultValue="3" @change="onChange" />
                        </a-col>  
                      </a-row>
                  </a-form-item>
                    <a-form-item>
                      <a-row>
                        <a-col :sm="7" :xs="24">
                            <span>磁盘大小</span>
                        </a-col>
                        <a-col :sm="10" :xs="24">
                          <a-input-number
                            :defaultValue="100"
                            :min="0"
                            :max="100000"
                            :formatter="value => `${value}GB`"
                            :parser="value => value.replace('GB', '')"
                          />
                        </a-col>  
                      </a-row>
                    </a-form-item>
                    <a-form-item>
                      <a-row>
                        <a-col :sm="7" :xs="24">
                          <a-checkbox>指定磁盘名称</a-checkbox>
                        </a-col>
                        <a-col :sm="10" :xs="24"><a-input /></a-col>  
                      </a-row>
                    </a-form-item>
                    <a-form-item>
                      <a-row>
                        <a-col :sm="7" :xs="24">
                          <a-checkbox>按需分配</a-checkbox>
                        </a-col>
                        <a-col :sm="10" :xs="24"><a-input /></a-col>  
                        <a-col :sm="4" :xs="24">
                          <div style="width:15px;height:15px;margin-left: 15px;">
                            <a-popover placement="right">
                                <template slot="content">
                                  <p>禁用按需分配将极大增加创建时间</p>
                                </template>
                                <a-button shape="circle" icon="question-circle" :size="small"/>
                              </a-popover>
                           </div>
                        </a-col>
                      </a-row>        
                    </a-form-item>
                  </a-form>
             </a-modal>
            </a-menu-item>
            <a-menu-divider />
            <a-menu-item key="1">
              <span v-if="record.address === '未映射'">
                <span @click="() => set_Address(true)">添加映射</span>
              </span>
              <span v-else>
                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="() => set_Address(true)">
                  <template slot="title">
                    <p>修改映射将影响当前正在使用该设备的主机及应用系统！</p>
                    <p>是否确认修改该映射？</p>
                    <a-checkbox>确认修改</a-checkbox>
                  </template>
                  <span>修改映射</span>
                </a-popconfirm>
              </span>
              <a-modal
                  title="为磁盘组XXX添加/修改映射"
                  centered
                  v-model="set_address"
                  @ok="() => set_address = false"
                  :width="500"
                  :visible="visible"
                >
                       
                     <a-form :form="form">
                         <a-form-item>
                           <a-row>
                             <a-col :sm="7" :xs="24">
                                 <span>编辑iSCSI映射</span>
                             </a-col>
                             <a-col :sm="10" :xs="24">
                               <a-select
                                     mode="multiple"
                                     :size="size"
                                     placeholder="Please select"
                                     :defaultValue="['a1', 'b2']"
                                     style="width: 200px"
                                     @change="handleChange"
                                     @popupScroll="popupScroll"
                                   >
                                     <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                                       {{(i + 9).toString(36) + i}}
                                     </a-select-option>
                                   </a-select>
                             </a-col>  
                           </a-row>
                       </a-form-item>
                       <a-form-item>
                           <a-row>
                             <a-col :sm="7" :xs="24">
                                 <span>编辑FC映射</span>
                             </a-col>
                             <a-col :sm="10" :xs="24">
                               <a-select
                                     mode="multiple"
                                     :size="size"
                                     placeholder="Please select"
                                     :defaultValue="['a1', 'b2']"
                                     style="width: 200px"
                                     @change="handleChange"
                                     @popupScroll="popupScroll"
                                   >
                                     <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                                       {{(i + 9).toString(36) + i}}
                                     </a-select-option>
                                   </a-select>
                             </a-col>  
                           </a-row>
                       </a-form-item>
                       <a-form-item>
                           <a-row>
                             <a-col :sm="7" :xs="24">
                                 <span>编辑IB映射</span>
                             </a-col>
                             <a-col :sm="10" :xs="24">
                               <a-select
                                     mode="multiple"
                                     :size="size"
                                     placeholder="Please select"
                                     :defaultValue="['a1', 'b2']"
                                     style="width: 200px"
                                     @change="handleChange"
                                     @popupScroll="popupScroll"
                                   >
                                     <a-select-option v-for="i in 25" :key="(i + 9).toString(36) + i">
                                       {{(i + 9).toString(36) + i}}
                                     </a-select-option>
                                   </a-select>
                             </a-col>  
                           </a-row>
                       </a-form-item>
                     </a-form>
                </a-modal>
            </a-menu-item>
            <a-menu-divider />
            <a-menu-item key="2" >
                <span @click="() => set_Property(true)">修改属性</span>
                <a-modal
                    title="编辑XXX磁盘组属性"
                    centered
                    v-model="set_property"
                    @ok="() => set_property = false"
                    :width="500"
                    :visible="visible"
                  >
                    <a-form-item>
                        <a-row>
                          <a-col :sm="8" :xs="24">
                            <a-checkbox>启用重复数据删除</a-checkbox>
                          </a-col>
                          <a-col :sm="8" :xs="24">
                            <a-checkbox>启用数据校验</a-checkbox>
                          </a-col>
                        </a-row>
                      </a-form-item>
                      <a-form-item>
                        <a-row>
                          <a-col :sm="8" :xs="24">
                            <a-checkbox>启用数据压缩</a-checkbox>
                          </a-col>
                          <a-col :sm="8" :xs="24">
                            <a-select defaultValue="高效" style="width: 120px" @change="handleChange">
                                  <a-select-option value="中等">中等</a-select-option>
                                  <a-select-option value="高等">高等</a-select-option>
                                  <a-select-option value="高效">高效</a-select-option>
                                </a-select>
                          </a-col>
                        </a-row>
                      </a-form-item>
                      <a-form-item>
                        <a-row>
                          <a-col :sm="8" :xs="24">
                            <a-checkbox>启用配额管理</a-checkbox>
                          </a-col>
                          <!-- <a-col :sm="7" :xs="24">
                            <span>最大分配空间</span>
                          </a-col> -->
                          <a-col :sm="10" :xs="24">
                            <div style="float:left">
                            <a-input-number
                              :defaultValue="100"
                              :min="0"
                              :max="100000"
                              :formatter="value => `${value}GB`"
                              :parser="value => value.replace('GB', '')"
                            />
                            </div>
                          <div style="width:15px;height:15px;margin-left: 15px;float:left;">
                            <a-popover placement="right">
                                <template slot="content">
                                  <span>不得小于4GB</span>
                                </template>
                                <a-button shape="circle" icon="question-circle" :size="small"/>
                              </a-popover>
                           </div>
                           </a-col>
                        </a-row>
                      </a-form-item>
                      <a-form-item>
                        <a-row>
                          <a-col :sm="8" :xs="24">
                            <span>块大小</span>
                          </a-col>
                          <a-col :sm="8" :xs="24">
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
                        <a-row>
                          <a-col :sm="8" :xs="24">
                            <span>同步写入</span>
                          </a-col>
                          <a-col :sm="8" :xs="24">
                            <a-select defaultValue="标准" style="width: 120px" @change="handleChange">
                                  <a-select-option value="总是">总是</a-select-option>
                                  <a-select-option value="标准">标准</a-select-option>
                                  <a-select-option value="关闭">关闭</a-select-option>
                                </a-select>
                          </a-col>
                        </a-row>
                      </a-form-item>
                      <a-form-item>
                        <a-row>
                          <a-col :sm="8" :xs="24">
                            <a-checkbox>授权用户</a-checkbox>
                          </a-col>
                          <a-col :sm="16" :xs="24">
                            <a-select  style="width: 120px" @change="handleChange">
                                </a-select>
                          </a-col>
                        </a-row>
                      </a-form-item>
                    </a-form>   
                  </a-modal>
            </a-menu-item>
            <a-menu-divider />
            <a-menu-item key="3">
              <span v-if="record.status === '4'">
                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="del-confirm">
                  <template slot="title">
                    <p>是否确定<b style="color:red">上线</b>该卷？</p>
                     <a-checkbox>确认修改</a-checkbox>
                  </template>
                  <span>卷上线</span>
                </a-popconfirm></span>
              <span v-else>
                <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="del-confirm">
                  <template slot="title">
                    <p>是否确定<b style="color:red">离线</b>该卷？</p>
                     <a-checkbox>确认修改</a-checkbox>
                  </template>
                  <span>卷离线</span>
                </a-popconfirm></span>
            </a-menu-item>
            <a-menu-divider />
            <a-menu-item key="4"> 
            <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="del-confirm">
             <template slot="title">
               <p>是否确定<b style="color:red">删除</b>该卷及下属磁盘？</p>
                <a-checkbox>确认删除</a-checkbox>
             </template>
             <span>删除卷</span>
           </a-popconfirm></a-menu-item>
          </a-menu>
        </a-dropdown>
    </span>
    <span slot="expandedRowRender" slot-scope="record" style="margin: 0">
      <a-table
          :columns="incolumns"
          :dataSource="indata_bj"
          :pagination="false"
        >
        <span slot="status" slot-scope="text">
          <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
        </span>
        <span slot="action" slot-scope="text, record">
          <a-dropdown :trigger="['click']">
              <a class="ant-dropdown-link" href="#"> 设置 <a-icon type="down" /> </a>
              <a-menu slot="overlay">
                <a-menu-item key="0">
                  <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="() => add_Disk(true)">
                    <template slot="title">
                   <p>是否确定恢复磁盘签名？</p>
                   <a-checkbox>确认恢复</a-checkbox>
                   <br>
                   <br>
                   <p>请选择需要恢复签名的系统类别：</p>
                   <a-radio-group name="radioGroup" :defaultValue="1">
                       <a-radio :value="1">Windows</a-radio>
                       <a-radio :value="2">Linux</a-radio>
                     </a-radio-group>
                 </template>
                 <a>恢复签名</a>
               </a-popconfirm>
              </a-menu-item>
              <a-sub-menu title="虚拟磁盘" key="1">
                <a-menu-item key="4"><a @click="() => to_Vdisk(true)">磁盘转换</a></a-menu-item>
                <a-modal
                   title="将XXX磁盘转换为虚拟磁盘"
                   centered
                   v-model="to_vdisk"
                   @ok="() => to_vdisk = false"
                    :width="500"
                    :visible="visible"
                  >
                     <a-form :form="form">
                       <a-form-item>
                         <a-row>
                           <a-col :sm="7" :xs="24">
                               <span>虚拟磁盘名称</span>
                           </a-col>
                           <a-col :sm="10" :xs="24">
                             <a-input />
                           </a-col>  
                         </a-row>
                     </a-form-item>
                       <a-form-item>
                         <a-row>
                           <a-col :sm="7" :xs="24">
                               <span>虚拟磁盘格式</span>
                           </a-col>
                           <a-col :sm="10" :xs="24">
                             <a-radio-group name="radioGroup" :defaultValue="1">
                                 <a-radio :value="1">Hyper-V(VDI)</a-radio>
                                 <a-radio :value="2">VMware(VMDK)</a-radio>
                                 <a-radio :value="3">vBox(VHD)</a-radio>
                                 <a-radio :value="4">KVM(QCOW2)</a-radio>
                               </a-radio-group>
                           </a-col>  
                         </a-row>
                       </a-form-item>
                     </a-form>
                </a-modal>
                <a-menu-item key="5"><a @click="() => vdisk_List(true)">转换清单</a></a-menu-item>
                <a-modal
                   title="XXX磁盘到虚拟磁盘转换任务清单"
                   centered
                   v-model="vdisk_list"
                   @ok="() => vdisk_list = false"
                    :width="500"
                  >
                  <div style="float:right"><a-button type="primary">清除已完成</a-button></div>
                     
                     <a-table :columns="vmlist_columns" :dataSource="vmlist_datas">
                       <span slot="progress" slot-scope="text, record">
                         <span v-if="record.progress==='100'">
                          <mini-progress color="rgb(29, 146, 40)" :target="100" :percentage="100" height="8px"/>
                        </span>
                        <span v-else-if="record.progress==='70'">
                          <mini-progress color="rgb(29, 146, 40)" :target="100" :percentage="70" height="8px"/>
                        </span>
                        <span v-else-if="record.progress==='0'">
                          <mini-progress color="rgb(29, 146, 40)" :target="100" :percentage="0" height="8px"/>
                        </span>
                       </span>
                       <span slot="action" slot-scope="text, record">
                         <span v-if="record.progress==='100'">
                           <a-dropdown :trigger="['click']">
                               <a class="ant-dropdown-link" href="#"> 操作 <a-icon type="down" /> </a>
                               <a-menu slot="overlay">
                                 <a-menu-item key="0">
                                   <a>下载</a>
                                 </a-menu-item>
                                 <a-menu-item key="1">
                                   <a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="del-confirm">
                                     <template slot="title">
                                       <p>是否确定<b style="color:red">删除</b>该虚拟磁盘？</p>
                                        <a-checkbox>确认删除</a-checkbox>
                                     </template>
                                     <span>删除</span>
                                   </a-popconfirm>
                                 </a-menu-item>
                               </a-menu>
                             </a-dropdown>
                         </span>
                         <span v-else>
                           {{record.progress}}%
                         </span>
                       </span>
                     </a-table>
                </a-modal>
              </a-sub-menu>
              <a-menu-item key="2">
                <a @click="() => mdisk_Capacity(true)">磁盘扩容</a>
                <a-modal
                   title="扩展XXX磁盘容量"
                   centered
                   v-model="mdisk_capacity"
                   @ok="() => mdisk_capacity = false"
                    :width="500"
                  >
                    <a-form :form="form">
                      <a-form-item>
                        <a-row>
                          <a-col :sm="7" :xs="24">
                              <span>现有容量</span>
                          </a-col>
                          <a-col :sm="5" :xs="24">
                            <a-input placeholder="1TB" disable="true"/>
                          </a-col>  
                        </a-row>
                    </a-form-item>
                      <a-form-item>
                        <a-row>
                          <a-col :sm="7" :xs="24">
                              <span>扩展后容量</span>
                          </a-col>
                          <a-col :sm="10" :xs="24">
                            <a-input-number
                                  :defaultValue="10"
                                  :min="0"
                                  :max="10000"
                                  :formatter="value => `${value}TB`"
                                  :parser="value => value.replace('TB', '')"
                                  @change="onChange"
                                />
                          </a-col>  
                        </a-row>
                      </a-form-item>
                    </a-form> 
                </a-modal>
              </a-menu-item>
              <a-menu-item key="3"><a-popconfirm placement="left" okText="Yes" cancelText="No" @confirm="() => add_Disk(true)">
                    <template slot="title">
                   <p>是否确定删除该磁盘？</p>
                   <a-checkbox>确认删除</a-checkbox>
                 </template>
                 <a>删除磁盘</a>
               </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>
      </a-table>
    </span>
  </a-table>
</template>
<script>
  import moment from 'moment'
  import { STable, Ellipsis } from '@/components'
  import { getRoleList, getServiceList} from '@/api/manage'
  import { ChartCard, MiniArea, MiniBar, MiniProgress, RankList, Bar, Trend, NumberInfo, MiniSmoothArea } from '@/components'
  import { mixinDevice } from '@/utils/mixin'
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
      text: '已离线'
    },
  }
  const vmlist_columns = [
    {
      title: '虚拟磁盘名称',
      dataIndex: 'name',
      scopedSlots: { customRender: 'name' },
      key: 'name',
    },
    {
      title: '进度',
      dataIndex: 'progress',
      scopedSlots: { customRender: 'progress' },
      key: 'progress',
    },
     {
       title: '操作',
       dataIndex: 'action',
       width: '100px',
       scopedSlots: { customRender: 'action' }
     }
  ];
  const vmlist_datas =[
    {
      key: '0',
      name: 'ToVMware',
      progress: '100'
    },
    {
      key: '1',
      name: 'ToKVM',
      progress: '70'
    },
    {
      key: '2',
      name: 'ToHyper-V',
      progress: '0'
    },
  ]
  const incolumns = [
      {
        title: '磁盘名称',
        dataIndex: 'device_name',
        key: 'device_name',
      },
      {
        title: '序列号',
        dataIndex: 'device_type',
        scopedSlots: { customRender: 'device_type' },
        key: 'device_type',
      },
      {
        title: '状态',
        dataIndex: 'status',
        scopedSlots: { customRender: 'status' },
        key: 'status',
      },
      {
        title: '容量',
        dataIndex: 'space',
        scopedSlots: { customRender: 'space' },
        key: 'space',
      },
     {
       title: '操作',
       dataIndex: 'action',
       scopedSlots: { customRender: 'action' }
     }
    ];
  const columns = [
    {
      title: '名称',
      dataIndex: 'name',
      slots: { title: 'customTitle' },
      scopedSlots: { customRender: 'name' },
    },
    {
      title: '存储池',
      dataIndex: 'pool',
      scopedSlots: { customRender: 'pool' },
      width: '100px',
    },
    {
      title: '状态',
      dataIndex: 'status',
      slots: { title: 'status' },
      scopedSlots: { customRender: 'status' },
      key: 'status',
      width: '110px',
    },
    {
      title: '映射',
      dataIndex: 'address',
      key: 'address',
      scopedSlots: { customRender: 'address' },
    },
    {
      title: '剩余容量',
      dataIndex: 'free',
      key: 'free',
      width: '100px',
      scopedSlots: { customRender: 'free' },
    },
    {
      title: '重删',
      dataIndex: 'Deduplication',
      key: 'Deduplication',
      width: '100px',
      scopedSlots: { customRender: 'Deduplication' },
    },
    {
      title: '压缩',
      dataIndex: 'Compression',
      key: 'Compression',
      width: '100px',
      scopedSlots: { customRender: 'Compression' },
    },
    {
      title: '块大小',
      dataIndex: 'Block',
      key: 'Block',
      width: '70px',
      scopedSlots: { customRender: 'Block' },
    },
    {
      title: '同步写入',
      dataIndex: 'SyncWrite',
      key: 'SyncWrite',
      width: '110px',
      scopedSlots: { customRender: 'SyncWrite' },
    },
    { 
      title: '操作',
      key: 'action',
      width: '100px',
      scopedSlots: { customRender: 'action' },
    },
  ];

  const data = [
    {
      key: '1',
      pool: 'SYSVOL',
      name: 'iSCSI',
      status: '1',
      address: ['iSCSI_1'],
      AdvanceSet: '已设置',
      free: '90%',
      Deduplication: '已开启',
      Compression: '未开启',
      Block: '128KB',
      SyncWrite: '标准',
    },
    {
      key: '2',
      pool: 'SYSVOL',
      name: 'Test',
      status: '0',
      address: ['iSCSI_2','iSCSI_3'],
      AdvanceSet: '已设置',
      free: '35%',
      Deduplication: '已开启',
      Compression: '高效',
      Block: '64KB',
      SyncWrite: '已关闭',
    },
    {
      key: '3',
      pool: 'TEST',
      name: 'New',
      status: '4',
      address: '未映射',
      AdvanceSet: '未设置',
      free: '40%',
      Deduplication: '已开启',
      Compression: '快速',
      Block: '256KB',
      SyncWrite: '标准',
    },
  ];
  export default {
    name: 'ListDiskArray',
    components: {
      STable,
      Ellipsis,
      MiniProgress,
    },
    data() {
      return {
        data,
        columns,
        incolumns,
        vmlist_columns,
        vmlist_datas,
        add_disk: false,
        set_address: false,
        set_property: false,
        to_vdisk: false,
        vdisk_list: false,
        mdisk_capacity: false,
        selectedKeys: [],
        selectedRowKeys: [],
        selectedRows: [],
        indata_bj:[
          {
            device_name: 'test_00000',
            device_type: '4500802673',
            status: '1',
            space: '1GB'
          },
          {
            device_name: 'test_00001',
            device_type: '4500802674',
            status: '0',
            space: '4GB'
          },
          {
            device_name: 'test_00002',
            device_type: '4500802675',
            status: '4',
            space: '1024GB'
          },
        ],
      };
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
      set_Property (set_property) {
        this.set_property = set_property
      },
      vdisk_List(vdisk_list){
        this.vdisk_list = vdisk_list
      },
      to_Vdisk(to_vdisk){
        this.to_vdisk = to_vdisk
      },
      add_Disk (add_disk){
        this.add_disk = add_disk
      },
      set_Address(set_address){
        this.set_address = set_address},
      mdisk_Capacity(mdisk_capacity){
        this.mdisk_capacity = mdisk_capacity
      },
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
  };
</script>
