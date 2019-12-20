<template>
  <div>
    <a-form style="margin: 40px auto 0;"><a-form-item>
      <result title="创建成功" :is-success="true"  style="max-width: 560px;">
        
      </result>
      
      </a-form-item>
      <a-form-item :wrapperCol="{span: 19, offset: 5}">
      <a-button style="margin-left: 8px"  type="primary" @click="finish">提交</a-button></a-form-item>
    </a-form>
  </div>
</template>

<script>
import { Result } from '@/components'

export default {
  name: 'Step3',
  components: {
    Result
  },
  data () {
    return {
      loading: false
    }
  },
  methods: {
    finish () {
      this.$router.push('/diskarray/listDiskArray')
    },
    toOrderList () {
      this.$router.push('/list/table-list')
    }
  }
}
</script>
<style lang="less" scoped>
  .information {
    line-height: 22px;

    .ant-row:not(:last-child) {
      margin-bottom: 24px;
    }
  }
  .money {
    font-family: "Helvetica Neue",sans-serif;
    font-weight: 500;
    font-size: 20px;
    line-height: 14px;
  }
</style>
