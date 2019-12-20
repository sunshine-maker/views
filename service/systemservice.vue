<template>
  <a-card>
  <a-table :columns="columns" :dataSource="data">
   <span slot="status" slot-scope="text">
     <a-badge :status="text | statusTypeFilter" :text="text | statusFilter" />
   </span>
   <span slot="operation" slot-scope="text, record">
     <span v-if="record.status >'0'">
       <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
         <template slot="title">
           <p>是否确定开启该服务？</p>
		   <a-checkbox>确认修改</a-checkbox>
         </template>
         <a>开启服务</a>
       </a-popconfirm>
     </span>
     <span v-else>
       <a-dropdown>
           <a class="ant-dropdown-link" href="#">
             更多操作 <a-icon type="down" />
           </a>
           <a-menu slot="overlay">
             <a-menu-item>
               <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                 <template slot="title">
                   <p>是否确定关闭该服务？</p>
				   <a-checkbox>确认修改</a-checkbox>
                 </template>
                 <a>关闭服务</a>
               </a-popconfirm>
             </a-menu-item>
             <a-menu-item>
               <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
                 <template slot="title">
                   <p>是否确定重启该服务？</p>
				   <a-checkbox>确认修改</a-checkbox>
                 </template>
                 <a>重启服务</a>
               </a-popconfirm>
             </a-menu-item>
             <a-menu-item>
               <span v-if="record.key < '2'">
                  <span v-if="record.key === '0'">
                     <span @click="() => Modify_Smb(true)">修改版本</span>
                  </span>
                   <span v-else-if="record.key === '1'">
                      <span @click="() => Modify_Nfs(true)">修改版本</span>
                   </span>
                </span>
                <span v-else>
                   <a disabled="True">修改版本</a>
                </span>
              </a-menu-item>
              </a-menu>
            </a-dropdown>
     </span>
   </span>
  </a-table>
  <a-modal
       title="修改NFS版本"
       centered
       v-model="modify_nfs"
       @ok="() => modify_nfs = false"
       :width="500"
     >
       <a-spin>
         <a-form>
           <a-form-item>
           <a-select
                  v-decorator="[
                    'select',
                    {rules: [{ required: true, message: '请选择NFS版本' }]}
                  ]"
                  placeholder="请选择NFS版本"
                >
                  <a-select-option value="3.0">
                    3.0
                  </a-select-option>
                  <a-select-option value="4.0">
                    4.0
                  </a-select-option>
                </a-select>
             </a-form-item>
         </a-form>
       </a-spin>
    </a-modal>
  <a-modal
       title="修改Samba版本"
       centered
       v-model="modify_smb"
       @ok="() => modify_smb = false"
       :width="500"
     >
       <a-spin>
         <a-form>
           <a-form-item >
           <a-select
                  v-decorator="[
                    'select',
                    {rules: [{ required: true, message: '请选择Samba版本' }]}
                  ]"
                  placeholder="请选择Samba版本"
                >
                  <a-select-option value="1.0">
                    1.0
                  </a-select-option>
                  <a-select-option value="2.0">
                    2.0
                  </a-select-option>
                  <a-select-option value="2.1">
                    2.1
                  </a-select-option>
                </a-select>
             </a-form-item>
         </a-form>
       </a-spin>
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
     text: '运行中'
   },
   1: {
     status: 'error',
     text: '已停止'
   },
   2: {
     status: 'processing',
     text: '异常'
   },
 }
  export default {
    name: 'Servics',
    components: {
      STable,
      Ellipsis
    },
    data() {
      return {
        modify_smb: false,
        modify_nfs: false,
        columns: [
          {
            title: '状态',
            dataIndex: 'status',
            scopedSlots: { customRender: 'status' },
          },
          {
            title: '服务名',
            dataIndex: 'service',
            scopedSlots: { customRender: 'service' },
          },
          {
            title: '版本',
            dataIndex: 'version',
            scopedSlots: { customRender: 'version' },
          },
          {
            title: '操作',
            dataIndex: 'operation',
            width: '150px',
            scopedSlots: { customRender: 'operation' },
          }
        ],
          data: [
            {
              key: '0',
              status: '0',
              version: '2.0',
              service: 'Samba',
            },
            {
              key: '1',
              status: '0',
              version: '3.0',
              service: 'NFS',
            },
            {
              key: '2',
              status: '0',
              version: '',
              service: 'iSCSI Initiator',
            },
            {
              key: '3',
              status: '1',
              version: '6.7',
              service: 'SSH',
            }
          ],
          
          Modify_Smb(modify_smb){
            this.modify_smb = modify_smb},
          Modify_Nfs(modify_nfs){
            this.modify_nfs = modify_nfs},
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
      handleChange(value, key, column) {
        const newData = [...this.data];
        const target = newData.filter(item => key === item.key)[0];
        if (target) {
          target[column] = value;
          this.data = newData;
        }
      },
      edit(key) {
        const newData = [...this.data];
        const target = newData.filter(item => key === item.key)[0];
        if (target) {
          target.editable = true;
          this.data = newData;
        }
      },
      save(key) {
        const newData = [...this.data];
        const target = newData.filter(item => key === item.key)[0];
        if (target) {
          delete target.editable;
          this.data = newData;
          this.cacheData = newData.map(item => ({ ...item }));
        }
      },
      cancel(key) {
        const newData = [...this.data];
        const target = newData.filter(item => key === item.key)[0];
        if (target) {
          Object.assign(target, this.cacheData.filter(item => key === item.key)[0]);
          delete target.editable;
          this.data = newData;
        }
      },
    },
  };
</script>
<style scoped>
  .editable-row-operations a {
    margin-right: 8px;
  }
</style>
