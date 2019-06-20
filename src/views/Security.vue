<template>
  <div class="xfn-security">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>安全管理</span>
      </div>
      <el-form
        :model="ruleForm"
        status-icon
        :rules="rules"
        label-width="100px"
        class="demo-ruleForm"
        ref="ruleForm"
      >
        <el-form-item label="旧密码" prop="pass">
          <el-input type="password" v-model="ruleForm.pass"></el-input>
        </el-form-item>
        <el-form-item label="新密码" prop="newPass">
          <el-input type="password" v-model="ruleForm.newPass"></el-input>
        </el-form-item>
        <el-form-item label="确认密码" prop="checkNewPass">
          <el-input type="password" v-model="ruleForm.checkNewPass"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>
<script>
export default {
  data() {
    var checkPass = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("旧密码不能为空"));
      }
      setTimeout(() => {
        if (value != "") {
          var url =
            this.$store.state.globalSettings.apiUrl +
            `/admin/login/${this.$store.state.adminName}/${value}`;
          this.$axios
            .get(url)
            .then(({ data }) => {
              if (data.code == 400) {
                return callback(new Error("原密码不正确！"));
              } else {
                return callback();
              }
            })
            .catch(err => {
              console.log(err);
            });
        }
      }, 1000);
    };
    var newPass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入新密码"));
      } else {
        if (this.ruleForm.checkNewPass !== "") {
          this.$refs.ruleForm.validateField("checkNewPass");
        }
        callback();
      }
    };
    var newPass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入新密码"));
      } else if (value !== this.ruleForm.newPass) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
    return {
      aname: "",
      ruleForm: {
        pass: "",
        newPass: "",
        checkNewPass: ""
      },
      rules: {
        newPass: [{ validator: newPass, trigger: "blur" }],
        checkNewPass: [{ validator: newPass2, trigger: "blur" }],
        pass: [{ validator: checkPass, trigger: "blur" }]
      }
    };
  },
  created() {
    this.aname = sessionStorage.getItem("userName");
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          var url = this.$store.state.globalSettings.apiUrl + "/admin";
          this.$axios
            .patch(url, {
              aname: this.aname,
              oldPwd: this.ruleForm.pass,
              newPwd: this.ruleForm.newPass
            })
            .then(({ data }) => {
              if (data.code == 200) {
                this.$alert("密码修改成功！", {
                  confirmButtonText: "确定",
                  callback: action => {
                    this.$message({
                      type: "info"
                    });
                    this.$router.go(0);
                    sessionStorage.removeItem("userName");
                  }
                });
              } else if (data.code == 401) {
                this.$message({
                  message: "新旧密码一样，未做修改!",
                  type: "warning"
                });
              }
            })
            .catch(error => {
              console.log(error);
            });
        } else {
          this.$message.error("密码修改失败！");
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

