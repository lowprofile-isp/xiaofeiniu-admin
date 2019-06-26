<template>
  <div class="xfn-dish-delete">
    <el-breadcrumb>
      <el-breadcrumb-item to="/main">首页</el-breadcrumb-item>
      <el-breadcrumb-item>菜品类别管理</el-breadcrumb-item>
      <el-breadcrumb-item>删除菜品</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>根据（菜品编号/菜品名称）删除菜品</span>
      </div>
      <el-form
        :model="formData"
        status-icon
        :rules="rules"
        label-width="150px"
        class="demo-ruleForm"
        ref="deleteData"
      >
        <el-form-item label="菜品编号/菜品名称" prop="dishInfo">
          <el-input v-model="formData.dishInfo"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="deleteDish('deleteData')">删除</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>
<script>
export default {
  data() {
    return {
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
      }
    };
  },
  methods: {
    deleteDish(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$confirm("您确定要删除此菜品吗?", "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
            center: true
          })
            .then(() => {
              var url =
                this.$store.state.globalSettings.apiUrl +
                "/admin/dish/" +
                this.formData.dishInfo;
              this.$axios
                .delete(url)
                .then(({ data }) => {
                  console.log(data)
                  if (data.code == 200) {
                    this.$message({
                      type: "success",
                      message: "删除成功!"
                    });
                  } else {
                    this.$message({
                      message: "没有此菜品!请核对菜品！",
                      type: "error"
                    });
                  }
                })
                .catch(error => {
                  console.log(error);
                });
            })
            .catch(() => {
              this.$message({
                type: "info",
                message: "已取消删除"
              });
            });
        } else {
          this.$message.error("删除菜品失败！请输入正确菜品！");
          return false;
        }
      });
    }
  }
};
</script>

