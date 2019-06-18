<template>
  <div class="xfn-table-info">
    <el-card shadow="hover">
      <div class="xfn-table" :style="{background:getTableColor(data.status)}">{{data.tid}}号桌：{{data.status|tableStatus}}</div>
      <el-button size="mini" type="success" plain @click="showTableDetail">详情</el-button>
      <el-button size="mini" type="danger" plain >修改</el-button>
    </el-card>
    <!--桌子详情对话框 -->
    <el-dialog :title="data.tid+'号桌详情'" :visible="dialogTableDetailVisible" :before-close="closeDetailTableDetail">
      <!-- 对话框主体 -->
      <el-tabs type="border-card" @tab-click="makeQRCode">
        <el-tab-pane><span slot="label">桌台状态</span>
          <el-form :model="data" label-width="100px">
            <el-form-item label="桌台状态：">
              <el-tag type="info">{{data.status|tableStatus}}</el-tag>
            </el-form-item>
            <el-form-item label="桌台名称：">
              <el-tag type="info">{{data.tname}}</el-tag>
            </el-form-item>
            <el-form-item label="类型：">
              <el-tag type="info">{{data.type}}</el-tag>
            </el-form-item>
            <el-form-item label="用餐人数：">
              <el-tag type="info"></el-tag>
            </el-form-item>
            <el-form-item label="下单人：">
              <el-tag type="info"></el-tag>
            </el-form-item>
            <el-form-item label="用餐时间：">
              <el-tag type="info"></el-tag>
            </el-form-item>
            <el-form-item label="用餐菜单：">
              <el-row>
                <el-col :span="8">
                  <el-card class="xfn-dish">
                    <img src="">
                    <el-badge :value="12" class="item dish-count" type="success"></el-badge>
                  </el-card>
                </el-col>
              </el-row>
            </el-form-item>
          </el-form>
        </el-tab-pane>
        <el-tab-pane label="桌台二维码">
          <!-- <canvas id="qrcanvas"></canvas> -->
          <img :src="qrcodeData">
          <p>提示：请将此二维码打印于对应桌台；顾客扫码即可点餐</p>
        </el-tab-pane>
      </el-tabs>
      <!-- 对话框尾部 -->
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogTableDetailVisible = false">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script> 
import { error } from 'util';
export default {
  data(){
    return {
      dialogTableDetailVisible:false,
      qrcodeData:''
    }
  },
  methods:{
    getTableColor(status){
      if(status==1){
        return '#67C23A'
      }else if(status==2){
        return '#E6A23C'
      }else if(status==3){
        return '#F56C6C'
      }else {
        return '#909399'
      }
    },
    showTableDetail(){
      this.dialogTableDetailVisible=true;
    },
    closeDetailTableDetail(){
      this.dialogTableDetailVisible=false;
    },
    makeQRCode(){
      //创建二维码——此方法不能再当前组件的mounted中调用，因为绘图必须得canvas在el-dialog中，对话框在组件加载时并不在DOM树上
      var qrcode = require('qrcode');
      var canvas = document.getElementById('qrcanvas');
      var tableUrl = this.$store.state.globalSettings.apiUrl + '/#/'+this.data.tid;
      // qrcode.toCanvas(
      //   canvas,
      //   tableUrl,
      //   {
      //     errorCorrectionLevel:'H',
      //     width:300
      //   }
      // )
      // 把绘制得到的图片二维码数据转为Base64编码的字符串
      qrcode.toDataURL(tableUrl,{errorCorrectionLevel:'H',width:300},(err,url)=>{
        this.qrcodeData = url;
      })
    }
  },
  props:['data']
}
</script>
<style lang="scss">
  .xfn-table-info{
    padding: 3px;
    text-align: center;
    .xfn-table{
      width: 90%;
      height: 100px;
      line-height: 100px;
      border-radius: 50%;
      box-shadow: 5px -5px 5px #888;
      margin: 0 auto 8px;
    }
    .el-dialog{
      .el-dialog__header{
        text-align: left;
      }
      .el-form-item__content{
        text-align: left;
      }
    }
  }
</style>


