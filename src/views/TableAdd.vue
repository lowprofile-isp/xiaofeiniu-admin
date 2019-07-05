<template>
  <div class="xfn-table-add">
    <el-breadcrumb>
      <el-breadcrumb-item to="/main">首页</el-breadcrumb-item>
      <el-breadcrumb-item>桌台管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加桌台</el-breadcrumb-item>
    </el-breadcrumb>
    <br>
    <br>
    <el-form label-width="100px" ref="formTable" :rules="rules1" :model="table">
      <el-form-item label="桌台名称" prop="tname">
        <el-input placeholder="请输入桌台名称" v-model="table.tname"></el-input>
      </el-form-item>
      <el-form-item label="桌台类别" prop="type">
        <el-radio-group v-model="table.type">
          <el-radio border label="2人桌"></el-radio>
          <el-radio border label="4人桌"></el-radio>
          <el-radio border label="6人桌"></el-radio>
          <el-radio border label="8人桌"></el-radio>
          <el-radio border label="12人桌"></el-radio>
          <el-radio border label="24人桌"></el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="桌台状态">
        <el-radio v-model="radio" label="1">空闲</el-radio>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="updateTable('formTable')">提交</el-button>
        <el-button @click="resetForm('formTable')">重填</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
export default {
  data() {
    return {
      radio: '1',
      table: {
        tid: null,
        tname: "",
        type: "",
        status: ""
      },
      rules1: {
        tname: [
          { required: true, message: "请输入桌台名称", trigger: "blur" },
          { min: 2, max: 5, message: "长度在 2 到 5 个字符", trigger: "blur" }
        ],
        type: [{ required: true, message: "请选择活动资源", trigger: "change" }]
      }
    };
  },
  methods:{
    updateTable(formName){
      this.$refs[formName].validate(valid => {
        if(valid){
          this.table.status = this.radio;
          var url = this.$store.state.globalSettings.apiUrl+'/admin/table';
          this.$axios.post(url,this.table).then(({data})=>{
            if(data.code==200){
              this.$alert("桌台添加成功！", {
                  confirmButtonText: "确定",
                  callback: action => {
                    this.$router.go(0);
                  }
                });
            }else{
              this.$message.error("桌台添加失败！请核对信息是否正确！");
            }
          }).catch(erro=>{
            console.log(error);
          })
        }else{
          this.$message.error("桌台添加失败！请核对信息是否正确！");
          return false;
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
};
</script>

