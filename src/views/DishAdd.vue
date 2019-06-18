<template>
  <div class="xfn-dish-add">
    <h1>添加菜品</h1>
    <el-form label-width="100px">
      <el-form-item label="菜品图片">
        <el-upload class="xfn-uploader" :action="uploadAction" :on-success="doUploadSucc">
          <img v-if="imageUrl" :src="imageUrl">
        </el-upload>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
export default {
  data(){
    return {
      imageUrl:'',//要显示的预览图地址
      uploadAction:this.$store.state.globalSettings.apiUrl + '/admin/dish/image' //可处理文件上传的数据API
    }
  },
  methods:{
    doUploadSucc(){
      //文件上传成功后，客户端得到响应消息之后处理函数
    }
  }
}
</script>

<style lang="scss">
  .xfn-dish-add{
    .xfn-uploader{
      .el-upload{
        border: 1px dotted #aaa;
        border-radius: 3px;
        cursor: pointer;
        width: 250px;
        height: 177px;
        overflow: hidden;
        &:hover{
          border-color: #409EFF;
        }
      }
    }
  }
</style>
