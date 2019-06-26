<template>
  <div class="xfn-dish-list">
    <el-breadcrumb>
      <el-breadcrumb-item to="/main">首页</el-breadcrumb-item>
      <el-breadcrumb-item>菜品类别管理</el-breadcrumb-item>
      <el-breadcrumb-item>菜品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-tabs type="border-card">
      <el-tab-pane v-for="(c,i) in dishList" :key="i">
        <span slot="label">
          <el-badge :value="c.dishList.length">{{c.cname}}</el-badge>
        </span>
        <el-row>
          <el-col v-for="(d,i) in c.dishList" :key="i" :xs="12" :sm="8" :md="6" :lg="4">
            <div class="xfn-dish" @mouseenter="showDishDetail(i)" @mouseleave="hideDishDetail()">
              <div v-if="ishow&&i==current" class="mask">
                <h4>{{d.title}}</h4>
                <p>{{d.detail}}</p>
                <span>￥:{{d.price}}</span>
              </div>
              <h4 v-else>{{d.title}}</h4>
              <img ref="img" :src="require('../../../xiaofeiniu-api/img/dish/'+d.imgUrl)">
            </div>
          </el-col>
        </el-row>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>
<script>
export default {
  data() {
    return {
      ishow: false,
      current: 0,
      dishList: []
    };
  },
  mounted() {
    var url = this.$store.state.globalSettings.apiUrl + "/admin/dish";
    this.$axios
      .get(url)
      .then(({ data }) => {
        this.dishList = data;
      })
      .catch(error => {
        console.log(error);
      });
  },
  methods: {
    showDishDetail(index) {
      this.ishow = true;
      this.current = index;
    },
    hideDishDetail() {
      this.ishow = true;
      this.current = null;
    }
  }
};
</script>
<style lang="scss">
.xfn-dish-list {
  color: #fff;
  .el-col {
    padding: 5px;
  }
  .xfn-dish {
    position: relative;
    overflow: hidden;
    & > h4 {
      position: absolute;
      padding: 5px;
    }
    img{
      display: block;
    }
    .mask {
      position: absolute;
      top: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.6);
      padding: 6px;
      padding-bottom: 0;
      h4 {
        color: #e00;
        margin: 0 0 5px;
      }
      p {
        color: #eee;
        font-size: 0.8em;
        margin: 0;
      }
      span {
        position: absolute;
        right: 1.5em;
        bottom: 1em;
        color: #fe0;
      }
    }
  }
  .el-tabs__item {
    padding: 3px 20px 0;
  }
  .el-badge__content {
    height: 11px;
    line-height: 11px;
  }
  .el-badge__content.is-fixed {
    top: 5px;
  }
}
</style>


