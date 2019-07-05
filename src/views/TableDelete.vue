<template>
  <div class="xfn-table-delete">
    <el-breadcrumb>
      <el-breadcrumb-item to="/main">首页</el-breadcrumb-item>
      <el-breadcrumb-item>桌台管理</el-breadcrumb-item>
      <el-breadcrumb-item>删除桌台</el-breadcrumb-item>
    </el-breadcrumb>
    <br>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>根据（桌台编号/桌台名称）删除桌台</span>
      </div>
      <el-form
        :model="formData"
        status-icon
        :rules="rules"
        label-width="150px"
        class="demo-ruleForm"
        ref="deleteData"
      >
        <el-form-item label="桌台编号/桌台名称" prop="tableInfo">
          <el-input v-model="formData.tableInfo" placeholder="请输入要删除的桌台编号或桌名"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="deleteTable('deleteData')">删除</el-button>
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
        tableInfo: ""
      },
      rules: {
        tableInfo: [
          {
            required: true,
            message: "桌台编号/桌台名称不能为空",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    deleteTable(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.$confirm("您确定要删除此桌台吗?", "提示", {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
            center: true
          })
            .then(() => {
              var url =
                this.$store.state.globalSettings.apiUrl +
                "/admin/table/" +
                this.formData.tableInfo;
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
                      message: "没有此桌台!请核对桌台信息！",
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
          this.$message.error("删除桌台失败！请输入正确桌台信息！");
          return false;
        }
      });
    }
  }
};
</script>

