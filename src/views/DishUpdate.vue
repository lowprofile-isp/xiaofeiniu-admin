
<template>
  <div class="xfn-dish-update">
    <el-breadcrumb>
      <el-breadcrumb-item to="/main">首页</el-breadcrumb-item>
      <el-breadcrumb-item>菜品类别管理</el-breadcrumb-item>
      <el-breadcrumb-item>修改菜品</el-breadcrumb-item>
    </el-breadcrumb>
    <br>
    <el-card v-if="formInfos.categoryId==''" class="box-card">
      <div slot="header" class="clearfix">
        <span>根据（菜品编号/菜品名称）修改菜品</span>
      </div>
      <el-form
        :model="formData"
        status-icon
        :rules="rules"
        label-width="150px"
        class="demo-ruleForm"
        ref="selectData"
      >
        <el-form-item label="菜品编号/菜品名称" prop="dishInfo">
          <el-input v-model="formData.dishInfo"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="selectDish('selectData')">提交</el-button>
        </el-form-item>
      </el-form>
    </el-card>
    <el-form v-else label-width="100px" ref="formInfos" :rules="rules1" :model="formInfos">
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
        <el-input placeholder="请输入菜品主标题" v-model="formInfos.title"></el-input>
      </el-form-item>
      <el-form-item label="所属类别">
        <el-radio-group v-model="radio1">
          <el-radio v-for="(c,i) in cnames" :key="i" :label="i" border>{{c.cname}}</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="价格" prop="price">
        <el-input placeholder="请输入菜品价格" v-model.number="formInfos.price"></el-input>
      </el-form-item>
      <el-form-item label="菜品描述" prop="detail">
        <el-input :rows="3" type="textarea" placeholder="请输入菜品描述" v-model="formInfos.detail"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="updateDish('formInfos')">提交</el-button>
        <el-button @click="resetForm('formInfos')">重填</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { setTimeout } from 'timers';
export default {
  data() {
    return {
      cnames: [],
      radio1: 0,
      formData: {
        dishInfo: ""
      },
      rules: {
        dishInfo: [
          {
            required: true,
            message: "菜品编号/菜品名称不能为空",
            trigger: "blur"
          }
        ]
      },
      rules1: {
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
      },
      imageUrl: '',
      uploadAction:
        this.$store.state.globalSettings.apiUrl + "/admin/dish/image", //可处理文件上传的数据API
      formInfos: {
        did:'',
        imgUrl: "", //菜品图片在服务器上的文件
        title: "",
        categoryId: "",
        price: "",
        detail: ""
      }
    };
  },
  mounted() {
    // var url = this.$store.state
    console.log(this.imageUrl)
  },
  methods: {
    doUploadSucc(res, file) {
      //文件上传成功后，客户端得到响应消息之后处理函数
      //res 服务器端返回的响应消息
      //file 从input[type=file] 中读取的第一个上传的文件对象
      this.formInfos.imgUrl = res.fileName;
      this.imageUrl = res.fileName;
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
    selectDish(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          var url =
            this.$store.state.globalSettings.apiUrl +
            "/admin/dish/dishDetail/" +
            this.formData.dishInfo;
          this.$axios.get(url).then(({ data }) => {
            if (data.code == 200) {
              var url1 =
                this.$store.state.globalSettings.apiUrl + "/admin/category";
              this.$axios
                .get(url1)
                .then(res => {
                  console.log(res.data)
                  this.cnames = res.data;
                  this.formInfos.categoryId = res.data.cid;
                })
                .catch(error => {
                  console.log(error);
                });
              this.formInfos.did = data.infos.did;
              this.formInfos.title = data.infos.title;
              this.formInfos.price = data.infos.price;
              this.formInfos.detail = data.infos.detail;
              this.formInfos.imgUrl = data.infos.imgUrl;
              // this.imageUrl = require("../../../xiaofeiniu-api/img/dish/"+data.infos.imgUrl);
              this.imageUrl = data.infos.imgUrl;
              this.radio1 = data.infos.categoryId - 1;
            } else {
              this.$message({
                message: "没有此菜品!请核对菜品！",
                type: "error"
              });
            }
          });
        } else {
          this.$message.error("修改菜品失败！请输入正确菜品！");
          return false;
        }
      });
    },
    updateDish(formName){
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.formInfos.categoryId = this.radio1+1;
          var url = this.$store.state.globalSettings.apiUrl + "/admin/dish/"+this.formInfos.did;
          this.$axios
            .put(url, this.formInfos)
            .then(({ data }) => {
              if (data.code == 200) {
                this.$alert("菜品修改成功！", {
                  confirmButtonText: "确定",
                  callback: action => {
                    this.$router.go(0);
                  }
                });
              }else if(data.code == 202){
                this.$message.warning("您没有作任何修改！");
              }else{
                this.$message.error("修改菜品失败！请核对信息是否正确！");
              }
            })
            .catch(error => {
              console.log(error);
            });
        } else {
          this.$message.error("修改菜品失败！请正确填写信息！");
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>
<style lang="scss">
  .xfn-dish-update{
    img{
      height: 100%;
    }
  }
</style>
