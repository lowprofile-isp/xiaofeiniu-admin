<template>
  <div class="xfn-dish-list">
    <el-tabs type="border-card">
      <el-tab-pane v-for="(c,i) in dishList" :key="i">
        <span slot="label">
          <el-badge :value="c.dishList.length">
            {{c.cname}}
          </el-badge>
        </span>
        <el-row>
          <el-col v-for="(d,i) in c.dishList" :key="i" :xs="12" :md="6" :lg="4" :xl="2">
            <h4>{{d.title}}</h4>
            <img>
          </el-col>
        </el-row>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>
<script>
export default {
  data(){
    return {
      dishList:[]
    }
  },
  mounted(){
    var url = this.$store.state.globalSettings.apiUrl+'/admin/dish';
    this.$axios.get(url).then(({data})=>{
      this.dishList = data;
    }).catch(error=>{
      console.log(error)
    })
  }
}
</script>
<style lang="scss">
  .xfn-dish-list{
    .el-tabs__item{
      padding: 3px 20px 0;
    }
    .el-badge__content{
      height: 11px;
      line-height: 11px;
    }
    .el-badge__content.is-fixed{
      top: 5px;
    }
  } 
</style>


