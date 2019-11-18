<template>
   <a-popconfirm placement="bottom" okText="Yes" cancelText="No" @confirm="confirm">
     <a-alert
           message="注意"
           description="重置火星舱后将恢复至出厂是的产品版本及设置,现有数据将丢失,请谨慎操作"
           type="warning"
         />
     <template slot="title">
       <p>将火星舱恢复出厂版本及设置？</p>
     </template>
     <a-button type="primary">恢复出厂</a-button>
   </a-popconfirm>
</template>

<script>
</script>

<style>
</style>
