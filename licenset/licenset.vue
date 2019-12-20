<template>
  <div class="page-header-index-wide">
    <a-card :bordered="false" :bodyStyle="{ padding: '16px 0', height: '100%' }" :style="{ height: '100%' }">
      <div class="account-settings-info-main" :class="device">
        <div>
          <div style="float:left;width:250px;margin:5px 0 0 15px;">
            <p>系统码：316D793476653C3E</p>
          </div>
          <div style="float:left;">
            <a-upload
                name="file"
                :multiple="true"
                action=""
                :headers="headers"
                @change="handleChange"
              >
                <a-button> <a-icon type="upload" /> 导入注册码 </a-button>
              </a-upload>
          </div>
        </div>
      </div>
      <div>
         <a-table :columns="columns" :dataSource="data" :pagination="false" />
      </div>
      <a-modal
      title="添加注册码"
      centered
      v-model="add_license"
      @ok="() => add_license = false"
      :width="500"
      :visible="visible"
      :confirmLoading="confirmLoading"
    >
      <template>
        <a-input placeholder="注册码" />
      </template>
    </a-modal>
    </a-card>
  </div>
  
</template>

<script>
import { PageView, RouteView } from '@/layouts'
import { mixinDevice } from '@/utils/mixin.js'
const columns = [
    {
      title: '已激活模块',
      dataIndex: 'modle',
      key: 'modle',
    },
    {
      title: '可用容量',
      dataIndex: 'capacity',
      key: 'capacity',
    },
  ];
  const data = [
    {
      key: '0',
      modle: '公共模块',
      children:[
        {
          key: '9',
          modle: '基本注册码'
        },
        {
          key: '10',
          modle: '总存储容量',
          capacity: '2TB'
        },
      ]
    },
    {
      key: '2',
      modle: 'VTL模块',
      children:[
        {
          key: '11',
          modle: 'VTL功能',
          capacity: '无限制'
        },
      ]
    },
    {
      key: '3',
      modle: 'VM模块',
      children:[
        {
          key: '12',
          modle: '虚拟机功能',
          capacity: '无限制'
        },
      ]
    },
    {
      key: '4',
      modle: '备份模块',
      children:[
        {
          key: '13',
          modle: '统一备份功能',
          capacity: '无限制'
        },
      ]
    },
    {
      key: '5',
      modle: 'NAS模块',
      children:[
        {
          key: '14',
          modle: 'NAS功能',
          capacity: '无限制'
        },
      ]
    },
    {
      key: '6',
      modle: 'CDP模块',
      children:[
        {
          key: '15',
          modle: 'CDP功能',
          capacity: '1TB'
        },
      ]
    },
    {
      key: '30',
      modle: 'UBackup模块',
      children:[
        {
          key: '16',
          modle: '友备云站功能',
          capacity: '无限制'
        },
      ]
    },
    {
      key: '7',
      modle: 'CDM备份模块',
      children:[
        {
          key: '17',
          modle: 'SQLServer',
          capacity: '无限制'
        },
        {
          key: '18',
          modle: 'MySQL',
          capacity: '无限制'
        },
        {
          key: '19',
          modle: 'Oracle',
          capacity: '无限制'
        },
        {
          key: '20',
          modle: '文件',
          capacity: '无限制'
        },
        {
          key: '21',
          modle: '华为云',
          capacity: '无限制'
        },
      ]
    }
  ];
export default {
  components: {
    RouteView,
    PageView
  },
  mixins: [mixinDevice],
  data () {
    return {
      // horizontal  inline
      mode: 'inline',
        data,
        columns,
      openKeys: [],
      selectedKeys: [],
      add_license: false,
      // cropper
      preview: {},
      option: {
        img: '/avatar2.jpg',
        info: true,
        size: 1,
        outputType: 'jpeg',
        canScale: false,
        autoCrop: true,
        // 只有自动截图开启 宽度高度才生效
        autoCropWidth: 180,
        autoCropHeight: 180,
        fixedBox: true,
        // 开启宽度和高度比例
        fixed: true,
        fixedNumber: [1, 1]
      },

      pageTitle: ''
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
  },
  watch: {
    '$route' (val) {
      this.updateMenu()
    }
  }
}
</script>

<style lang="less" scoped>
  button{
      border-radius: 0;
  }
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
