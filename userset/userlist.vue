<template>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="8" :sm="24">
              <a-form-item label="搜索类型">
                <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                  <a-select-option value="0">用户</a-select-option>
                  <a-select-option value="1">用户组</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="用户/用户组名称">
                <a-input v-model="queryParam.id" placeholder=""/>
              </a-form-item>
            </a-col>
            <a-col :md="!advanced && 8 || 24" :sm="24">
              <span class="table-page-search-submitButtons" :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
                <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
                <a-button style="margin-left: 8px" @click="() => queryParam = {}">重置</a-button>
              </span>
            </a-col>
          </a-row>
        </a-form>
      </div>
      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="() => Add_Userm(true)">新建用户组</a-button>
        <a-dropdown v-if="hasSelected">
          <a-menu slot="overlay">
            <a-menu-item key="1">
              <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
               <template slot="title">
                 <p>是否确定删除该用户组及组下所有用户？</p>
               </template>
               <a><a-icon type="delete" />删除用户</a>
             </a-popconfirm></a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px">
            批量操作 <a-icon type="down" />
          </a-button>
        </a-dropdown>
      </div>
      <div>
      	<a-alert message="用户权限不可超越用户组权限,如需要添加更多权限则仅需要添加组权限,该用户即可继承权限,或者调整用户所在分组" banner closable />
      </div>
      <div>
         <a-table :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}":columns="columns" :dataSource="data" :pagination="false" >
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
             <span v-if="record.key > '14'">
              <a-dropdown :trigger="['click']">
                 <a class="ant-dropdown-link" > 配置<a-icon type="down" /> </a>
                 <a-menu slot="overlay">
                    <a-menu-item key="0">
                      <span><a @click="() => setNet_Ip(true)">修改密码</a></span>
                    </a-menu-item>
                    <a-menu-item key="1">
                      <span><a @click="() => setNet_Dns(true)">修改有效期</a></span>
                    </a-menu-item>
                    <a-menu-item key="2">
                      <span>
                        <a @click="() => modifyNet_Router(true)">修改权限</a>
                      </span>
                    </a-menu-item>
                    <a-menu-item key="3">
                      <span>
                        <a @click="() => modifyNet_Router(true)">添加用户</a>
                      </span>
                    </a-menu-item>
                 </a-menu>
               </a-dropdown>
             </span>
             <span v-else-if="record.key < '13'">
               <a-dropdown :trigger="['click']">
                  <a class="ant-dropdown-link" > 配置<a-icon type="down" /> </a>
                  <a-menu slot="overlay">
                     <a-menu-item key="0">
                       <span><a @click="() => setNet_Ip(true)">修改密码</a></span>
                     </a-menu-item>
                     <a-menu-item key="1">
                       <span><a @click="() => setNet_Dns(true)">修改有效期</a></span>
                     </a-menu-item>
                     <a-menu-item key="2">
                       <span>
                         <a @click="() => modifyNet_Router(true)">修改权限</a>
                       </span>
                     </a-menu-item>
                     <a-menu-item key="3">
                       <span>
                         <a @click="() => modifyNet_Router(true)">更换分组</a>
                       </span>
                     </a-menu-item>
                  </a-menu>
                </a-dropdown>
             </span>
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
      width: '200px',
    },
    {
      title: '描述',
      dataIndex: 'describe',
      scopedSlots: { customRender: 'describe' },
      width: '200px',
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
  ];
  const data = [
    
    {
      key: '19',
      modle: 'test',
      describe: '测试账户组',
      permission: ['VTL_2TB','NAS_1TB','CDM','CDP_1TB'],
      children:[
        {
          key: '10',
          modle: 'test',
          describe: '测试账户1',
          permission: ['CDM'],
        },{
          key: '11',
          modle: 'test1',
          describe: '测试账户2',
          permission: ['CDM','CDP_1TB'],
        },
      ]
    },
    {
      key: '18',
      modle: 'ppp',
      describe: '研发部测试账户组',
      permission: ['VTL_2TB','NAS','CDM','CDP_1TB','远程控制','自定义1'],
      children:[
        {
          key: '9',
          modle: 'pp_t',
          describe: '研发测试账户1',
          permission: ['CDM_Oracle','NAS'],
        },{
          key: '8',
          modle: 'pp_m',
          describe: '研发账户2',
          permission: ['CDM_SQL','远程控制','自定义1'],
        },
      ]
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
