<template>
  <div class="page-header-index-wide">
    <a-card :bordered="false" :bodyStyle="{ padding: '16px 0', height: '100%' }" :style="{ height: '100%' }">
      <a-form :form="form" @submit="handleSubmit">
        <a-form-item label="工作模式" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }">
          <a-select
            v-decorator="[
              'gender',
              { rules: [{ required: true, message: '请选择工作模式!' }] },
            ]"
            placeholder="请选择工作模式"
            @change="handleSelectChange"
          >
            <a-select-option value="单机">
              单机
            </a-select-option>
            <a-select-option value="多机">
              多机
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="节点属性" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }">
          <a-select
            v-decorator="[
              'gender',
              { rules: [{ required: true, message: '请选择节点属性!' }] },
            ]"
            placeholder="请选择节点属性"
            @change="handleSelectChange"
          >
            <a-select-option value="汇报节点">
              汇报节点
            </a-select-option>
            <a-select-option value="汇总节点">
              汇总节点
            </a-select-option>
            <a-select-option value="汇报&汇总节点">
              汇报&汇总节点
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="Replication信息" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }">
          <a-select
            v-decorator="[
              'gender',
              { rules: [{ required: true, message: '请选择是否显示Replication信息!' }] },
            ]"
            placeholder="请选择节是否显示Replication信息"
            @change="handleSelectChange"
          >
            <a-select-option value="显示">
              显示
            </a-select-option>
            <a-select-option value="不显示">
              不显示
            </a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item :wrapper-col="{ span: 12, offset: 6 }">
              <a-button type="primary" html-type="submit">
                确定
              </a-button>
            </a-form-item>
      </a-form>
    </a-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formLayout: 'horizontal',
      form: this.$form.createForm(this, { name: 'coordinated' }),
    };
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        if (!err) {
          console.log('Received values of form: ', values);
        }
      });
    },
    handleSelectChange(value) {
      console.log(value);
      this.form.setFieldsValue({
        note: `Hi, ${value === 'male' ? 'man' : 'lady'}!`,
      });
    },
  },
};
</script>