<template>
  <div class="xfn-category-list">
    <el-breadcrumb>
      <el-breadcrumb-item to="/main">首页</el-breadcrumb-item>
      <el-breadcrumb-item>菜品类别</el-breadcrumb-item>
    </el-breadcrumb>
    <br>
    <el-button type="primary" style="margin:.7em;padding: 7px 15px;" plain @click="addCategory">
      <i class="el-icon-plus"></i> 添加新的菜品类别
    </el-button>
    <el-table :data="categoryList" style="width: 100%" stripe border>
      <el-table-column prop="cid" label="编号"></el-table-column>
      <el-table-column prop="cname" label="名称"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="{row,$index}">
          <el-button size="mini" type="success" plain @click="updateCategory(row,$index)">修改</el-button>
          <el-button size="mini" type="danger" plain @click="deleteCategory(row,$index)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
export default {
  data() {
    return {
      categoryList: []
    };
  },
  mounted() {
    var url = this.$store.state.globalSettings.apiUrl + "/admin/category";
    this.$axios
      .get(url)
      .then(res => {
        this.categoryList = res.data;
        console.log(res, data);
      })
      .catch(error => {
        console.log(error);
      });
  },
  methods: {
    addCategory(){
      this.$prompt('请输入新菜品类别的名称：','添加菜品类别',{type:'info',inputPlaceholder:'添加菜品类别'}).then(({value})=>{
        //获得用户的输入，调用数据API添加到数据库
        var url = this.$store.state.globalSettings.apiUrl + '/admin/category';
        this.$axios.post(url,{cname:value}).then(res=>{
          console.log(res)
          if(res.data.code==200){
            // 数据库添加成功
            this.$message.success('新的类别添加成功！')
            //模型数据中添加新的类别
            this.categoryList.push({cid:res.data.cid,cname:value})
          }else{
            this.$message.error('新的类别添加过程出错：'+res.data.msg)
          }
        }).catch(error=>{
          console.log(error)
        })
      }).catch(()=>{
        this.$message({type:'info',message:'已取消添加！'})
      })
    },
    updateCategory(c, i) {
      console.log(c, i);
      this.$prompt('请输入您的名称：','修改菜品类别',{type:'info',inputValue:c.cname}).then(({value})=>{
        //获得用户的输入，调用数据API添加到数据库
        var url = this.$store.state.globalSettings.apiUrl + '/admin/category';
        this.$axios.update(url).then(res=>{

        }).catch(error=>{
          console.log(error)
        })
      }).catch(()=>{
        this.$message({type:'info',message:'已取消修改！'})
      })
    },
    deleteCategory(c, i) {
      this.$confirm("删除操作不可撤销！您确定吗？", "提示", { type: "warning" })
        .then(() => {
          var url =
            this.$store.state.globalSettings.apiUrl +
            "/admin/category/" +
            c.cid;
          this.$axios
            .delete(url)
            .then(res => {
              if (res.data.code == 200) {
                this.categoryList.splice(i, 1);
                this.$message.success("菜品类别删除成功！");
              } else {
                this.$message.error("菜品类别删除出错：");
              }
            })
            .catch(error => {
              console.log(error);
            });
        })
        .catch(error => {
          this.$message({type:'info',message:'已取消删除！'})
        });
    }
  }
};
</script>

