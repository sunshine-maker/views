<template>
    <a-card :bordered="false">
      <div>
      	<a-alert message="*********" banner closable />
      </div>
      <div>
         <a-table :columns="columns" :dataSource="data" :pagination="false" >
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
               <a href="javascript:;">密码管理</a>
           </span>
        </a-table>
      </div>
      
    </a-card>
  
</template>

<script>
import { PageView, RouteView } from '@/layouts'
import { mixinDevice } from '@/utils/mixin.js'
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
// import StepByStepModal from './modules/StepByStepModal'
// import CreateForm from './modules/CreateForm'
import { getRoleList, getServiceList} from '@/api/manage'
const columns = [
    {
      title: '用户组及用户',
      dataIndex: 'modle',
      key: 'modle',
    },
    {
      title: '描述',
      dataIndex: 'describe',
      scopedSlots: { customRender: 'describe' }
    },
    {
      title: '权限',
      dataIndex: 'permission',
      scopedSlots: { customRender: 'permission' }
    },
    {
      title: '操作',
      dataIndex: 'action',
      width: '90px',
      scopedSlots: { customRender: 'action' }
    }
  ];
  const data = [
    {
      key: '20',
      modle: 'admin',
      describe: '超级管理员',
      permission: ['super_admin'],
    },
    {
      key: '21',
      modle: 'dev_admin',
      describe: '系统管理员',
      permission: ['system_admin'],
    },
    {
      key: '22',
      modle: 'job_admin',
      describe: '安全保密管理员',
      permission: ['security_admin'],
    },
    {
      key: '23',
      modle: 'audit_admin',
      describe: '安全审计员',
      permission: ['auditor'],
    },
  ];
export default {
  components: {
    RouteView,
    PageView,
    name: 'userList',
    components: {
      STable,
      Ellipsis
    }
  },
  mixins: [mixinDevice],
  data () {
    return {
      // horizontal  inline
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      mode: 'inline',
        data,
        columns,
      openKeys: [],
      // selectedKeys: [],
      // add_license: false,
      // cropper
      preview: {},
      selectedRowKeys: [],
      selectedRows: [],
      option: {
        rowSelection: {
          selectedRowKeys: this.selectedRowKeys,
          onChange: this.onSelectChange
        }
      },
      
    }
  },
  created () {
    this.updateMenu()
  },
  methods: {
    onOpenChange (openKeys) {
      this.openKeys = openKeys
    },
    updateMenu () {
      const routes = this.$route.matched.concat()
      this.selectedKeys = [ routes.pop().path ]
    },
    
    Add_License(add_license){
      this.add_license = add_license
    },
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
              rowSelection: {
                selectedRowKeys: this.selectedRowKeys,
                onChange: this.onSelectChange,
                getCheckboxProps: record => ({
                  props: {
                    disabled: record.modle === 'admin', // Column configuration not to be checked  
                    key: record.key,
                  }
                })
              }
            }
          } else {
            this.options = {
              rowSelection: null
            }
          }
        },
  },
  computed: {
      
      hasSelected() {
        return this.selectedRowKeys.length > 0
      }
    },
  watch: {
    '$route' (val) {
      this.updateMenu()
    }
  }
}
</script>

<style lang="less" scoped>
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
      width: 150px;
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
