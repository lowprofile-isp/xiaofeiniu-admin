<template>
  <div id="app">
    <!-- <h6>欢迎 ：{{$store.state.globalSettings}}</h6> -->
    <router-view/>
  </div>
</template>
<script>
// https://github.com/Lccitem/xiaofeiniu-admin.git
// https://github.com/Lccitem/xiaofeiniu-api.git
// https://github.com/Lccitem/xiaofeiniu-app.git
export default {
  mounted() {
    console.log('APP mounted...')
    //当前组件挂载完成后需要异步请求全局配置数据
    var url = this.$store.state.globalSettings.apiUrl+'/admin/settings';
    this.$axios.get(url).then((res)=>{
      console.log(res)
      this.$store.commit('setGlobalSettings',res.data);
    }).catch((err)=>{
      console.log(err)
    })
  },
}
</script>
<style lang="scss">
  #app {
    color: #303133;
    font-family: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;
  }
</style>
