<template>
  <div class="xfn-dish-add">
    <h1>添加菜品</h1>
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
  data() {
    return {
      cnames: [],
      radio1: 0,
      imageUrl: "", //要显示的预览图地址
      uploadAction:
        this.$store.state.globalSettings.apiUrl + "/admin/dish/image", //可处理文件上传的数据API
      formData: {
        imgUrl: "", //菜品图片在服务器上的文件
        title: "",
        categoryId: "",
        price: "",
        detail: ""
      },
      rules: {
        imgUrl: [
          { required: true, message: "请添加菜品图片", trigger: "blur" }
        ],
        title: [
          { required: true, message: "请填写菜品标题", trigger: "blur" },
          { min: 1, max: 10, message: "长度在 1 到 10 个字符", trigger: "blur" }
        ],
        price: [
          { required: true, message: "请填写菜品价格", trigger: "blur" },
          { type: "number", message: "价格必须为数字值" }
        ],
        detail: [
          { required: true, message: "请填写活动形式", trigger: "blur" }
        ]
      }
    };
  },
  methods: {
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
    addDish(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.formData.categoryId = this.radio1 + 1;
          console.log(this.formData);
          var url = this.$store.state.globalSettings.apiUrl + "/admin/dish";
          this.$axios
            .post(url, this.formData)
            .then(({ data }) => {
              if (data.code == 200) {
                this.$alert("菜品添加成功！", {
                  confirmButtonText: "确定",
                  callback: action => {
                    this.$message({
                      type: "info"
                    });
                    this.$router.go(0);
                  }
                });
              }
            })
            .catch(error => {
              console.log(error);
            });
        } else {
          this.$message.error("添加菜品失败！");
          return false;
        }
      });
      // this.
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  },
  mounted() {
    var url = this.$store.state.globalSettings.apiUrl + "/admin/category";
    this.$axios
      .get(url)
      .then(res => {
        this.cnames = res.data;
        this.formData.categoryId = res.data.cid;
      })
      .catch(error => {
        console.log(error);
      });
  }
};
</script>

<style lang="scss">
.xfn-dish-add {
  img{
    height: 100%;
  }
}
</style>
