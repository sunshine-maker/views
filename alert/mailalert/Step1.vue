<template>
  <div>
    <a-form :form="form" style="max-width: 500px; margin: 40px auto 0;">
      <a-form-item>
        <div>
          <div style="width:50px;float:left;margin-left: 55px;"><a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked/></div>
          <div style="width:300px;float:left"><p>启用邮件告警</p></div>
        </div>
      </a-form-item>
      <a-form-item
        label="发件人地址"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
      <a-input v-decorator="['sendmail', { rules: [{required: true, message: '发件人地址必须填写'}] }]"/>
      
      </a-form-item>
      <a-form-item
        label="SMTP服务器"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
      <a-input v-decorator="['smtpserver', { rules: [{required: true, message: 'SMTP服务器必须填写'}] }]"/>
      
      </a-form-item>
      <a-form-item
        label="SMTP端口"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
      <a-input  placeholder="25" v-decorator="['smtprot', { rules: [{required: true, message: 'SMTP端口必须填写'}] }]"/>
      </a-form-item>
      <a-form-item>
        <div>
          <div style="width:50px;float:left;margin-left: 55px;"><a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked/></div>
          <div style="width:300px;float:left"><p>启用SSL验证</p></div>
          </br>
          <div style="width:50px;float:left;margin-left: 55px;"><a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked/></div>
          <div style="width:300px;float:left"><p>SMTP服务器身份验证</p></div>
        </div>
      </a-form-item>
      <a-form-item
        label="账号"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
      <a-input/>
      </a-form-item>
      <a-form-item
        label="密码"
        :labelCol="labelCol"
        :wrapperCol="wrapperCol"
      >
      <a-input type="password" placeholder="Password" />
      </a-form-item>
      <a-form-item :wrapperCol="{span: 19, offset: 5}">
        <a-button type="primary" @click="nextStep">下一步<a-icon type="right" /></a-button>>
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
