<template>
  <div class="page-header-index-wide">
    <a-card :bordered="false" :bodyStyle="{ padding: '16px 0', height: '100%' }" :style="{ height: '100%' }">
      <a-form :form="form" @submit="handleSubmit">
        <a-form-item label="保留周期" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }">
          <a-row>
            <a-col :sm="4" :xs="24">
              <a-input-number :min="1" :max="100000" :defaultValue="3" />
            </a-col>
            <a-col :sm="6" :xs="24">
              <a-select
                  v-decorator="[
                    'round',
                    { rules: [{ required: true, message: '请选择周期单位!' }] },
                  ]"
                  placeholder="请选择周期单位"
                  @change="handleSelectChange"
                >
                <a-select-option value="周">
                  周
                </a-select-option>
                <a-select-option value="月">
                  月
                </a-select-option>
                <a-select-option value="年">
                  年
                </a-select-option>
              </a-select>
            </a-col>
          </a-row>  
          
        </a-form-item>
        <a-form-item label="可使用空间" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }">
          <a-row>
            <a-col :sm="4" :xs="24">
              <a-input-number :min="1" :max="100000" :defaultValue="3" />
            </a-col>
            <a-col :sm="6" :xs="24">
              <a-select
                  v-decorator="[
                    'space',
                    { rules: [{ required: true, message: '请选择空间单位!' }] },
                  ]"
                  placeholder="请选择空间单位"
                  @change="handleSelectChange"
                >
                <a-select-option value="MB">
                  MB
                </a-select-option>
                <a-select-option value="GB">
                  TB
                </a-select-option>
                <a-select-option value="TB">
                  GB
                </a-select-option>
              </a-select>
            </a-col>
          </a-row>  
        </a-form-item>
        <a-form-item label="冲突处理" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }">
          <a-row>
            <a-col :sm="10" :xs="24">
          <a-select
            v-decorator="[
              'collide',
              { rules: [{ required: true, message: '请选择是否显示冲突处理方案!' }] },
            ]"
            placeholder="请选择是否显示冲突处理方案"
            @change="handleSelectChange"
          >
            <a-select-option value="1">
              以保留周期优先
            </a-select-option>
            <a-select-option value="2">
              以总空间优先
            </a-select-option>
            <a-select-option value="3">
              提出告警，由管理员处理
            </a-select-option>
          </a-select>
            </a-col>
          </a-row>  
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