<template>
  <div class="xfn-dish-update">
    <el-breadcrumb>
      <el-breadcrumb-item to="/main">首页</el-breadcrumb-item>
      <el-breadcrumb-item>菜品类别管理</el-breadcrumb-item>
      <el-breadcrumb-item>修改菜品</el-breadcrumb-item>
    </el-breadcrumb>
    <el-form label-width="100px" ref="formData" :rules="rules" :model="formData">
      <el-form-item label="菜品图片" prop="imgUrl">
        <el-upload
          class="xfn-uploader"
          :action="uploadAction"
          :on-success="doUploadSucc"
          name="dishImg"
          :before-upload="beforeAvatarUpload"
        >
          <img v-if="imageUrl" :src="imageUrl">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          <div slot="tip" class="el-upload__tip">只能上传jpg文件，且不超过500kb</div>
        </el-upload>
      </el-form-item>
      <el-form-item label="主标题" prop="title">
        <el-input placeholder="请输入菜品主标题" v-model="formData.title"></el-input>
      </el-form-item>
      <el-form-item label="所属类别">
        <el-radio-group v-model="radio1">
          <el-radio v-for="(c,i) in cnames" :key="i" :label="i" border>{{c.cname}}</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="价格" prop="price">
        <el-input placeholder="请输入菜品价格" v-model.number="formData.price"></el-input>
      </el-form-item>
      <el-form-item label="菜品描述" prop="detail">
        <el-input :rows="3" type="textarea" placeholder="请输入菜品描述" v-model="formData.detail"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="addDish('formData')">提交</el-button>
        <el-button @click="resetForm('formData')">重填</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
export default {
  data(){
    return{
      imageUrl:"",
      formData: {
        imgUrl: "", //菜品图片在服务器上的文件
        title: "",
        categoryId: "",
        price: "",
        detail: ""
      },
    }
  },
  mounted(){
    // var url = this.$store.state
  },
  methods:{
    doUploadSucc(res, file) {
      //文件上传成功后，客户端得到响应消息之后处理函数
      //res 服务器端返回的响应消息
      //file 从input[type=file] 中读取的第一个上传的文件对象
      this.formData.imgUrl = res.fileName;
      this.imageUrl = URL.createObjectURL(file.raw); //把上传的文件编码转为DataURL字符串
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === "image/jpeg";
      const isLt = file.size / 1024 / 1024 < 0.5;

      if (!isJPG) {
        this.$message.error("上传头像图片只能是 JPG 格式!");
      }
      if (!isLt) {
        this.$message.error("上传头像图片大小不能超过 500kb!");
      }
      return isJPG && isLt;
    },
  }
}
</script>
