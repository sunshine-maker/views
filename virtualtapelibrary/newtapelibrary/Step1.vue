<template>
  <div style="text-align:center">
    <a-form :form="form">
      <a-form-item>
        <a-row>
          <a-col :sm="7" :xs="24">
              <span>磁带库名称</span>
          </a-col>
          <a-col :sm="10" :xs="24">
                <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入主机组名称'}]}]" />
          </a-col>  
        </a-row>
        </a-form-item>
        <a-form-item>
          <a-row>
            <a-col :sm="7" :xs="24">
                <span>磁带库类型</span>
            </a-col>
            <a-col :sm="10" :xs="24">
                  <a-select defaultValue="ADIC Scalar 24" >
                        <a-select-option value="ADIC Scalar 24">ADIC Scalar 24</a-select-option>
                        <a-select-option value="ADIC Scalar 100">ADIC Scalar 100</a-select-option>
                        <a-select-option value="ADIC Scalar 1000">ADIC Scalar 1000</a-select-option>
                      </a-select>
            </a-col>  
          </a-row>
          </a-form-item>
          <a-form-item>
            <a-row>
              <a-col :sm="7" :xs="24">
                  <span>驱动器型号</span>
              </a-col>
              <a-col :sm="10" :xs="24">
                <a-select defaultValue="IBM ULTRIUM-TD1" >
                  <a-select-option value="IBM ULTRIUM-TD1">IBM ULTRIUM-TD1</a-select-option>
                  <a-select-option value="IBM ULTRIUM-TD2">IBM ULTRIUM-TD2</a-select-option>
                  <a-select-option value="IBM ULTRIUM-TD3">IBM ULTRIUM-TD3</a-select-option>
                </a-select>
              </a-col>  
            </a-row>
            </a-form-item>
            <a-form-item>
              <a-row>
                <a-col :sm="7" :xs="24">
                    <span>驱动器数量</span>
                </a-col>
                <a-col :sm="6" :xs="24">
                      <a-input-number id="inputNumber" :min="1" :max="255" :defaultValue="3" />
                </a-col>  
                <a-col :sm="5" :xs="24">
                      （1-255）
                </a-col>  
              </a-row>
              </a-form-item>
            <a-form-item>
              <a-row>
                <a-col :sm="7" :xs="24">
                    <span>出入槽位数</span>
                </a-col>
                <a-col :sm="6" :xs="24">
                      <a-input-number id="inputNumber" :min="1" :max="128" :defaultValue="3" />
                </a-col>  
                <a-col :sm="5" :xs="24">
                      （1-128）
                </a-col>  
              </a-row>
              </a-form-item>
            <a-form-item>
              <a-row>
                <a-col :sm="7" :xs="24">
                    <span>槽位数</span>
                </a-col>
                <a-col :sm="6" :xs="24">
                      <a-input-number id="inputNumber" :min="1" :max="1000" :defaultValue="3" />
                </a-col>  
                <a-col :sm="5" :xs="24">
                      （1-1000）
                </a-col>  
              </a-row>
              </a-form-item>
      <a-form-item :wrapperCol="{span: 19, offset: 5}">
        <a-button type="primary" @click="nextStep">下一步</a-button>
        <a-button style="margin-left: 8px"  type="primary" @click="finish">提交</a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script>
export default {
  name: 'Step1',
  data () {
    return {
      labelCol: { lg: { span: 5 }, sm: { span: 5 } },
      wrapperCol: { lg: { span: 19 }, sm: { span: 19 } },
      form: this.$form.createForm(this)
    }
  },
  methods: {
    finish () {
      this.$router.push('/virtualtapelibrary/tapelibrary')
    },
    nextStep () {
      const { form: { validateFields } } = this
      // 先校验，通过表单校验后，才进入下一步
      validateFields((err, values) => {
        if (!err) {
          this.$emit('nextStep')
        }
      })
    }
  }
}
</script>

<style lang="less" scoped>
.step-form-style-desc {
  padding: 0 56px;
  color: rgba(0,0,0,.45);

  h3 {
    margin: 0 0 12px;
    color: rgba(0,0,0,.45);
    font-size: 16px;
    line-height: 32px;
  }

  h4 {
    margin: 0 0 4px;
    color: rgba(0,0,0,.45);
    font-size: 14px;
    line-height: 22px;
  }

  p {
    margin-top: 0;
    margin-bottom: 12px;
    line-height: 22px;
  }
}
</style>
