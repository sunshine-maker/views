<template>
  <a-card :bordered="false">
    <a-form :form="form">
      <a-form-item>
        <a-row :gutter="20">
          <a-col :sm="7" :xs="24">
              <span>共享目录名称</span>
          </a-col>
          <a-col :sm="13" :xs="24">
                <a-input v-decorator="['desc', {rules: [{required: true, min: 5, message: '请注入主机组名称'}]}]" />
          </a-col>  
        </a-row>
        </a-form-item>
        <a-form-item>
          <a-row :gutter="20">
            <a-col :sm="7" :xs="24">
                <span>所在卷组</span>
            </a-col>
            <a-col :sm="13" :xs="24">
              <a-select defaultValue="SYSVOL" >
                <a-select-option value="SYSVOL">SYSVOL</a-select-option>
                <a-select-option value="TEST">TEST</a-select-option>
              </a-select>
            </a-col>  
          </a-row>
          </a-form-item>
          <a-form-item>
              <a-row :gutter="20">
                <a-col :sm="7" :xs="24">
                  <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用重复数据删除
                </a-col>
                <a-col :sm="7" :xs="24">
                  <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用数据校验
                </a-col>
              </a-row>
            </a-form-item>
            <a-form-item>
              <a-row :gutter="20">
                <a-col :sm="7" :xs="24">
                  <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用数据压缩
                </a-col>
                <a-col :sm="7" :xs="24">
                  <a-select defaultValue="高效" style="width: 120px" @change="handleChange">
                        <a-select-option value="中等">中等</a-select-option>
                        <a-select-option value="高等">高等</a-select-option>
                        <a-select-option value="高效">高效</a-select-option>
                      </a-select>
                </a-col>
              </a-row>
            </a-form-item>
            <a-form-item>
              <a-row :gutter="20">
                <a-col :sm="7" :xs="24">
                  <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />启用配额管理
                </a-col>
                <!-- <a-col :sm="7" :xs="24">
                  <span>最大分配空间</span>
                </a-col> -->
                <a-col :sm="5" :xs="24">
                  <a-input-number
                    :defaultValue="100"
                    :min="0"
                    :max="100000"
                    :formatter="value => `${value}GB`"
                    :parser="value => value.replace('GB', '')"
                  />
                  </div>
                </a-col>  
                <a-col :sm="8" :xs="24">
                  <a-popover placement="right">
                    <template slot="content">
                      <span>不得小于4GB</span>
                    </template>
                     <a-icon type="question-circle" />
                  </a-popover>
                 </a-col>
              </a-row>
            </a-form-item>
            <a-form-item>
              <a-row :gutter="20">
                <a-col :sm="7" :xs="24">
                  <span>块大小</span>
                </a-col>
                <a-col :sm="13" :xs="24">
                  <a-select defaultValue="128KB" style="width: 120px" @change="handleChange">
                    <a-select-option value="4">4KB</a-select-option>
                    <a-select-option value="8">8KB</a-select-option>
                    <a-select-option value="16">16KB</a-select-option>
                    <a-select-option value="32">32KB</a-select-option>
                    <a-select-option value="64">64KB</a-select-option>
                    <a-select-option value="128">128KB</a-select-option>
                  </a-select>
                </a-col>
              </a-row>
            </a-form-item>
            <a-form-item>
              <a-row :gutter="20">
                <a-col :sm="7" :xs="24">
                  <span>同步写入</span>
                </a-col>
                <a-col :sm="13" :xs="24">
                  <a-select defaultValue="标准" style="width: 120px" @change="handleChange">
                    <a-select-option value="总是">总是</a-select-option>
                    <a-select-option value="标准">标准</a-select-option>
                    <a-select-option value="关闭">关闭</a-select-option>
                  </a-select>
                </a-col>
              </a-row>
            </a-form-item>
            <a-form-item>
              <a-row :gutter="20">
                <a-col :sm="7" :xs="24">
                  <a-switch checkedChildren="开" unCheckedChildren="关" defaultunChecked />授权用户
                </a-col>
                <a-col :sm="13" :xs="24">
                  <a-select  style="width: 120px" @change="handleChange" />
                </a-col>
              </a-row>
            </a-form-item>
      <a-form-item :wrapperCol="{span: 19, offset: 5}">
        <a-button type="primary" @click="nextStep">下一步<a-icon type="right" /></a-button>
        <a-button style="margin-left: 8px"  type="primary" @click="finish">提交</a-button>
      </a-form-item>
    </a-form>
  </a-card>
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
