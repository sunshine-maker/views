<template>
  <a-form layout="vertical">
    <a-form-item>
  <a-transfer
    :dataSource="mockData"
    showSearch
    :listStyle="{
      width: '250px',
      height: '300px',
    }"
    :operations="['删除', '恢复']"
    :targetKeys="targetKeys"
    @change="handleChange"
    :render="item=>`${item.title}-${item.description}`"
  >
    <span slot="notFoundContent">
      没数据
    </span>
  </a-transfer>
  <div style="margin:5px 0 0 515px;">
    <a-popconfirm placement="top" okText="Yes" cancelText="No" @confirm="confirm">
            <template slot="title">
              <p>是否确认删除右侧清单中的虚拟机模板</p>
            </template>
            <a-button>删除</a-button>
          </a-popconfirm>
    </div>
  
  </a-form-item>
  <a-divider />
    <a-form-item>
      <div>
        <div style="float:left;width:300px">
          <p>上传新的虚拟机模板</p>
        </div>
        <div style="float:right;width:300px">
          <a slot="actions"><a-upload
            action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
            :multiple="true"
            :fileList="fileList"
            @change="handleChange"
          >
            <a-button> <a-icon type="upload" /> 选择升级包 </a-button>
          </a-upload>
          </a>
        </div>
      </div>
    </a-form-item>  
      <a-form-item>
        <a-button type="primary">确定</a-button>
      </a-form-item>
      </a-form>
</template>
<script>
  export default {
    data() {
      return {
        mockData: [],
        targetKeys: [],
      };
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
            title: `Windows/Linux`,
            description: `${i + 1}`,
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
      handleChange(targetKeys, direction, moveKeys) {
        console.log(targetKeys, direction, moveKeys);
        this.targetKeys = targetKeys;
      },
    },
  };
</script>
