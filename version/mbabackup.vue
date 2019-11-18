<template>
  <a-form layout="vertical">
    <a-form-item>
  <a-list itemLayout="horizontal" :dataSource="data">
    <a-list-item slot="renderItem" slot-scope="item, index">
      <a slot="actions"><a-upload
        action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
        :multiple="true"
        :fileList="fileList"
        @change="handleChange"
      >
        <a-button> <a-icon type="upload" /> 选择升级包 </a-button>
      </a-upload></a>
      <a-list-item-meta>
        <a slot="title">{{item.title}}</a>
      </a-list-item-meta>
    </a-list-item>
  </a-list>
  </a-form-item>
  <a-form-item>
    <a-button type="primary">提交</a-button>
    <a-button style="margin-left: 8px">取消</a-button>
  </a-form-item>
  </a-form>
</template>
<script>
  const data = [
    {
      title: '当前版本：xxxxx',
    },
    {
      title: '当前版本：xxxxx',
    },
    {
      title: '当前版本：xxxxx',
    },
    {
      title: '当前版本：xxxxx',
    },
  ];
  export default {
    data() {
      return {
        data,
        fileList: [
          {
            uid: '-1',
            name: 'xxx.tar',
            status: 'done',
            // url: 'http://www.baidu.com/xxx.png',
          },
        ],
      };
    },
    methods: {
      handleChange(info) {
        let fileList = [...info.fileList];
    
        // 1. Limit the number of uploaded files
        //    Only to show two recent uploaded files, and old ones will be replaced by the new
        fileList = fileList.slice(-2);
    
        // 2. read from response and show file link
        fileList = fileList.map(file => {
          if (file.response) {
            // Component will show file.url as link
            file.url = file.response.url;
          }
          return file;
        });
    
        this.fileList = fileList;
      },
    },
  };
</script>
<style></style>
