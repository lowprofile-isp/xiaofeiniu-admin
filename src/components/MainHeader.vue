<template>
  <div class="xfn-main-header">
    <h3 class="xfn-title">{{$store.state.globalSettings.appName}}管理后台</h3>
    <div class="xfn-right">
      当前用户：{{$store.state.adminName}}
    </div>
  </div>
</template>
<script>
export default {
  
}
</script>

<style lang="scss">
  .xfn-main-header{

  }
</style>

