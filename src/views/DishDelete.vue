<template>
  <div class="xfn-dish-delete">
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
      formData:{
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
  methods:{
    deleteDish(formName){
      this.$refs[formName].validate(valid => {
        if(valid){
          var url = this.$store.state.globalSettings.apiUrl+'/admin/dish';
          this.$axios.delete(url,{'disInfo':this.dishInfo}).then(({data})=>{
            console.log(data)
          }).catch(error=>{
            console.log(error);
          })
        }else {
          this.$message.error("删除菜品失败！");
          return false;
        }
      })
    }
  }
};
</script>

