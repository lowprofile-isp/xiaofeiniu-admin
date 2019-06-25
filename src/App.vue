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
  beforeMount(){
    if(sessionStorage.getItem('userName')){
      this.$store.commit('setAdminName',sessionStorage.getItem('userName'))
    }else{
      this.$store.commit('setAdminName',null)
    }
  },
  mounted() {
    console.log('APP mounted...')
    //当前组件挂载完成后需要异步请求全局配置数据
    var url = this.$store.state.globalSettings.apiUrl+'/admin/settings';
    this.$axios.get(url).then((res)=>{
      this.$store.commit('setGlobalSettings',res.data);
    }).catch((err)=>{
      console.log(err)
    })
  },
}
</script>
<style lang="scss">
  *{margin: 0;padding: 0;}
  #app {
    color: #303133;
    // font-family: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;
    font-family: "Microsoft YaHei";
  }
  .main{
    padding: 20px;
  }
  .el-message-box__input {
    padding-top: 12px !important;
  }
  .el-icon-info+.el-message-box__message{
    padding-left: 0 !important;
  }
  .el-message{
    background-color: #C0C4CC !important; 
  }
  .el-message__content{
    color: #fff !important;
    letter-spacing: 2px;
  }
  .el-card{
    margin-top: 20px;
  }
  .xfn-uploader {
    .el-upload {
      border: 1px dashed #d9d9d9;
      border-radius: 6px;
      cursor: pointer;
      width: 218px;
      height: 154px;
      overflow: hidden;
      &:hover {
        border-color: #409eff;
      }
      .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 178px;
        height: 154px;
        line-height: 154px;
        text-align: center;
      }
    }
  }
  img {
    max-width: 100%;
  }
  // .el-col .el-col-24{
  //   padding: 3px;
  // }
</style>
