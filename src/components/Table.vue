<template>
  <div class="xfn-table-info">
    <el-card shadow="hover">
      <div
        class="xfn-table"
        :style="{background:getTableColor(tableChildList.status)}"
      >{{tableChildList.tid}}号桌：{{tableChildList.status|tableStatus}}</div>
      <el-button size="mini" type="success" plain @click="showTableDetail(tableChildList.status)">详情</el-button>
      <el-button size="mini" type="danger" plain @click="updateTableDetail()">修改</el-button>
    </el-card>
    <!--桌子详情对话框 -->
    <el-dialog
      :title="tableChildList.tid+'号桌详情'"
      :visible="dialogTableDetailVisible"
      :before-close="closeDetailTableDetail"
    >
      <!-- 对话框主体 -->
      <el-tabs type="border-card" @tab-click="makeQRCode">
        <el-tab-pane>
          <span slot="label">桌台状态</span>
          <el-form :model="tableChildList" label-width="100px">
            <el-form-item label="桌台状态：">
              <el-tag type="info">{{tableChildList.status|tableStatus}}</el-tag>
            </el-form-item>
            <div v-if="yuding">
              <el-form-item label="预约人：">
                <el-tag type="info">{{reservationList.contactName}}</el-tag>
              </el-form-item>
              <el-form-item label="用餐人数：">
                <el-tag type="info">{{reservationList.customerCount}}人</el-tag>
              </el-form-item>
              <el-form-item label="联系电话：">
                <el-tag type="info">{{reservationList.phone}}</el-tag>
              </el-form-item>
              <el-form-item label="联系时间：">
                <el-tag type="info">{{reservationList.contactTime | datetime}}</el-tag>
              </el-form-item>
              <el-form-item label="用餐时间：">
                <el-tag type="info">{{reservationList.dinnerTime | datetime}}</el-tag>
              </el-form-item>
            </div>
            <div v-if="zhanyong">
              <el-form-item label="桌台名称：">
                <el-tag type="info">{{tableChildList.tname}}</el-tag>
              </el-form-item>
              <el-form-item label="类型：">
                <el-tag type="info">{{tableChildList.type}}</el-tag>
              </el-form-item>
              <el-form-item label="用餐人数：">
                <el-tag type="info">{{orderList.customerCount}}人</el-tag>
              </el-form-item>
              <el-form-item label="下单人：">
                <el-tag type="info">{{orderDetail[0].customerName}}</el-tag>
              </el-form-item>
              <el-form-item label="用餐时间：">
                <el-tag type="info">{{orderList.startTime | datetime}}</el-tag>
              </el-form-item>
              <el-form-item label="用餐菜单：">
                <el-row>
                  <el-col :span="8">
                    <el-card class="xfn-dish">
                      <img />
                      <el-badge :value="12" class="item dish-count" type="success"></el-badge>
                    </el-card>
                  </el-col>
                </el-row>
              </el-form-item>
            </div>
            <div v-if="qita">
              <p>{{qitaInfo.str}}</p>
            </div>
          </el-form>
        </el-tab-pane>
        <el-tab-pane label="桌台二维码">
          <img :src="qrcodeData" />
          <p>提示：请将此二维码打印于对应桌台；顾客扫码即可点餐</p>
        </el-tab-pane>
      </el-tabs>
      <!-- 对话框尾部 -->
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogTableDetailVisible = false">确 定</el-button>
      </div>
    </el-dialog>

    <el-dialog :title="tableChildList.tid+'桌状态修改'" :visible.sync="dialogVisible">
      <!-- 表单2验证 -->
      <el-form :model="reservationList" :rules="rules1" ref="tableForm">
        <el-form-item label="桌台状态：">
          <el-radio-group v-model="radio1">
            <el-radio :label="1" border>空闲</el-radio>
            <el-radio :label="2" border>预定</el-radio>
            <el-radio :label="3" border>占用</el-radio>
            <el-radio :label="4" border>其他</el-radio>
          </el-radio-group>
        </el-form-item>
        <div v-if="radio1 == '2'">
          <el-form-item prop="dinnerTime" label="预定时间：">
            <el-date-picker
              v-model="reservationList.dinnerTime"
              type="datetime"
              placeholder="选择日期时间"
            >{{reservationList.dinnerTime | datetime}}</el-date-picker>
          </el-form-item>
          <el-form-item label="预约人：" prop="contactName">
            <el-input v-model="reservationList.contactName" placeholder="预约联系人"></el-input>
          </el-form-item>
          <el-form-item label="用餐人数：" prop="customerCount">
            <el-input v-model.number="reservationList.customerCount" placeholder="用餐人数"></el-input>
          </el-form-item>
          <el-form-item label="联系电话：" prop="phone">
            <el-input v-model="reservationList.phone" placeholder="预约人联系电话"></el-input>
          </el-form-item>
        </div>
        <!-- 表单2验证end -->

        <!-- 表单3验证 -->
        <el-form :model="orderList" v-if="radio1 == '3'" :rules="rules2" ref="tableForm2">
          <el-form-item prop="startTime" label="用餐时间：">
            <el-date-picker
              v-model="orderList.startTime"
              type="datetime"
              placeholder="选择日期时间"
            >{{orderList.startTime | datetime}}</el-date-picker>
          </el-form-item>
          <el-form-item label="用餐人数：" prop="customerCount">
            <el-input v-model.number="orderList.customerCount" placeholder="用餐人数"></el-input>
          </el-form-item>
        </el-form>
        <!-- 表单3验证end -->

        <!-- 表单4验证 -->
        <el-form :model="qitaInfo" v-if="radio1 == '4'" :rules="rules3" ref="tableForm3">
          <el-form-item label="桌台详情：" prop="str">
            <el-input v-model="qitaInfo.str" placeholder="请填写桌台详细信息">{{qitaInfo.str}}</el-input>
          </el-form-item>
        </el-form>
        <!-- 表单4验证end -->

        <el-form-item>
          <el-button
            type="primary"
            @click="updatePost(radio1,'tableForm','tableForm2','tableForm3')"
          >确 定</el-button>
          <el-button @click="closeForm">取 消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script>
import { error } from "util";
import { type } from "os";
export default {
  data() {
    var phone = (rule, value, callback) => {    //手机号正则验证
      if (!value) {
        return callback(new Error("手机号不能为空"));
      }
      let phone_tes = /^1[3456789]\d{9}$/;
      if (!phone_tes.test(value)) {
        return callback(new Error("请输入正确手机号"));
      }
      callback();
    };
    return {
      radio1: 1,                          //单选框选项状态
      yuding: false,                      //预定桌台
      zhanyong: false,                    //占用桌台
      qita: false,                        //其他桌台
      qitaInfo: {                         //其他桌台信息
        str: "提示：本桌台处于维修状态"
      },
      dialogTableDetailVisible: false,    //详情模态框
      dialogVisible: false,               //修改模态框
      qrcodeData: "",                     //二维码
      tableChildList: this.data,          //桌台列表
      reservationList: {                  //预约列表
        rid: null,
        contactName: "",
        phone: "",
        customerCount: "",
        contactTime: "",
        dinnerTime: "",
        tableId: ""
      },
      orderList: {               //订单列表
        oid: null,
        startTime: "",
        endTime: "",
        customerCount: "",
        tableId: ""
      },
      orderDetail: [],           //订单详情列表
      dishListId:[],
      newTime: "",               //当前本地时间

      // 预约桌台表单验证
      rules1: {
        dinnerTime: [
          {
            type: "date",
            required: true,
            message: "请选择日期",
            trigger: "change"
          }
        ],
        contactName: [
          { required: true, message: "请填写预约人名称", trigger: "blur" },
          { min: 1, max: 5, message: "长度在 1 到 5 个字符", trigger: "blur" }
        ],
        phone: [{ required: true, validator: phone, trigger: "blur" }],
        customerCount: [
          { required: true, message: "请填写用餐人数", trigger: "blur" },
          {
            min: 1,
            max: 20,
            type: "number",
            message: "用餐人必须是1-20位，且必须填写数字",
            trigger: "blur"
          }
        ]
      },
      // 占用桌台表单验证
      rules2: {
        customerCount: [
          { required: true, message: "请填写用餐人数", trigger: "blur" },
          {
            min: 1,
            max: 20,
            type: "number",
            message: "用餐人必须是1-20位，且必须填写数字",
            trigger: "blur"
          }
        ],
        startTime: [
          {
            type: "date",
            required: true,
            message: "请选择日期",
            trigger: "change"
          }
        ]
      },
      // 其他桌台表单验证
      rules3: {
        str: {
          required: true,
          message: "请填写桌台详细信息",
          trigger: "blur"
        }
      }
    };
  },
  methods: {
    // 根据status返回不同的颜色值
    getTableColor(status) {
      if (status == 1) {
        return "#67C23A";
      } else if (status == 2) {
        return "#E6A23C";
      } else if (status == 3) {
        return "#F56C6C";
      } else {
        return "#909399";
      }
    },

    // 根据status返回不同的状态
    getTableInfos(status) {
      if (status == 1) {
        return 1;
      } else if (status == 2) {
        return 2;
      } else if (status == 3) {
        return 3;
      } else {
        return false;
      }
    },

    // 点击详情按钮
    showTableDetail($status) {
      if ($status == 1) {
        this.dialogTableDetailVisible = true;
      }
      if ($status == 2) {
        this.dialogTableDetailVisible = true;
        this.yuding = true;
        this.zhanyong = false;
        this.qita = false;
        var url =
          this.$store.state.globalSettings.apiUrl +
          "/admin/table/reservation/" +
          this.tableChildList.tid;
        this.$axios
          .get(url)
          .then(({ data }) => {
            this.reservationList.rid = data.rid;
            this.reservationList.contactName = data.contactName;
            this.reservationList.phone = data.phone;
            this.reservationList.customerCount = data.customerCount;
            this.reservationList.contactTime = data.contactTime;
            this.reservationList.dinnerTime = data.dinnerTime;
          })
          .catch(error => {
            console.log(error);
          });
      }
      if ($status == 3) {
        var url =
          this.$store.state.globalSettings.apiUrl +
          "/admin/table/order/" +
          this.tableChildList.tid;
        this.$axios
          .get(url)
          .then(({ data }) => {
            console.log(data);
            if (data.code == 400) {
              this.dialogTableDetailVisible = false;
              this.$alert("暂无订单详情！", {
                confirmButtonText: "确定"
              });
            } else {
              this.dialogTableDetailVisible = true;
              this.yuding = false;
              this.zhanyong = true;
              this.qita = false;
              this.orderList.startTime = data.order.startTime;
              this.orderList.endTime = data.order.endTime;
              this.orderList.tableId = data.order.tableId;
              this.orderList.customerCount = data.order.customerCount;
              this.orderList.oid = data.order.oid;
              this.orderDetail = data.order_detail;
              this.dishListId = data.order_detail_dish_did;
              this.$axios.post(this.$store.state.globalSettings.apiUrl+'/admin/dish/orderDetailDish',{'dishId':this.dishListId}).then(({data})=>{
                // console.log(data)
              }).catch(error=>{
                console.log(error)
              })
            }
          })
          .catch(error => {
            console.log(error);
          });
      }
      if ($status == 4) {
        this.dialogTableDetailVisible = true;
        this.qita = true;
        this.yuding = false;
        this.zhanyong = false;
      }
    },

    // 点击修改按钮
    updateTableDetail() {
      this.newTime = Number(new Date());
      this.orderList.startTime = Number(new Date());
      this.dialogVisible = true;
    },

    // 点击修改确定按钮
    updatePost($status, formName, formName2, formName3) {
      // 空闲桌台
      if ($status == 1) {
        var url =
          this.$store.state.globalSettings.apiUrl +
          "/admin/table/" +
          this.tableChildList.tid;
        this.$axios
          .delete(url)
          .then(({ data }) => {
            if (data.order.code == 200 || data.reservation.code == 200) {
              this.tableChildList.status = 1;
              this.dialogVisible = false;
              this.$alert("桌台修改成功！", {
                confirmButtonText: "确定"
              });
            } else {
              this.$message.error("修改桌台失败！请核对信息是否正确！");
            }
          })
          .catch(error => {
            console.log(error);
          });
      }
      // 预约桌台
      if ($status == 2) {
        this.reservationList.contactTime = this.newTime;
        this.reservationList.tableId = this.tableChildList.tid;
        this.$refs[formName].validate(valid => {
          if (valid) {
            const h = this.$createElement;
            this.$msgbox({
              title: "消息",
              message: h("p", null, [
                h("span", null, "您确定要 "),
                h("i", { style: "color: teal" }, "修改桌台"),
                h("span", null, " 吗？")
              ]),
              showCancelButton: true,
              confirmButtonText: "确定",
              cancelButtonText: "取消",
              beforeClose: (action, instance, done) => {
                if (action === "confirm") {
                  instance.confirmButtonLoading = true;
                  instance.confirmButtonText = "修改中...";
                  var url =
                    this.$store.state.globalSettings.apiUrl +
                    "/admin/table/reservation/" +
                    this.tableChildList.tid;
                  this.reservationList.dinnerTime = Number(
                    this.reservationList.dinnerTime
                  );
                  this.$axios
                    .put(url, this.reservationList)
                    .then(({ data }) => {
                      if (data.code == 200) {
                        this.tableChildList.status = 2;
                        this.dialogVisible = false;
                      } else {
                        this.$message.error(
                          "修改桌台失败！请核对信息是否正确！"
                        );
                      }
                    })
                    .catch(error => {
                      console.log(error);
                    });
                  setTimeout(() => {
                    done();
                    setTimeout(() => {
                      instance.confirmButtonLoading = false;
                    }, 300);
                  }, 1000);
                } else {
                  done();
                }
              }
            })
              .then(action => {
                this.$message({
                  type: "info",
                  message: "桌台修改成功！"
                });
              })
              .catch(error => {
                console.log(error);
              });
          } else {
            this.$message.error("修改桌台失败！请正确填写信息！");
            return false;
          }
        });
      }
      // 占用桌台
      if ($status == 3) {
        this.orderList.tableId = this.tableChildList.tid;
        this.orderList.endTime = this.orderList.startTime + 2400000;
        this.$refs[formName2].validate(valid => {
          if (valid) {
            var url =
              this.$store.state.globalSettings.apiUrl +
              "/admin/table/order/" +
              this.tableChildList.tid;
            this.$axios
              .put(url, this.orderList)
              .then(({ data }) => {
                if (data.code == 200) {
                  this.tableChildList.status = 3;
                  this.dialogVisible = false;
                  this.$alert("桌台修改成功！", {
                    confirmButtonText: "确定",
                    callback: action => {
                      this.$router.go(0);
                    }
                  });
                } else {
                  this.$message.error("修改桌台失败！请核对信息是否正确！");
                }
              })
              .catch(error => {
                console.log(error);
              });
          } else {
            this.$message.error("修改桌台失败！请正确填写信息！");
            return false;
          }
        });
      }
      // 其他桌台
      if ($status == 4) {
        this.$refs[formName3].validate(valid => {
          if (valid) {
            var url =
              this.$store.state.globalSettings.apiUrl +
              "/admin/table/qitainfo/" +
              this.tableChildList.tid;
            this.$axios
              .put(url)
              .then(({ data }) => {
                if (data.code == 200) {
                  this.tableChildList.status = 4;
                  this.dialogVisible = false;
                  this.$alert("桌台修改成功！", {
                    confirmButtonText: "确定"
                  });
                } else {
                  this.$message.error("修改桌台失败！请填写桌台详情！");
                }
              })
              .catch(error => {
                console.log(error);
              });
          } else {
            this.$message.error("修改桌台失败！请填写桌台详情！");
          }
        });
      }
    },

    // 点击dialogVisible取消按钮
    closeForm() {
      this.dialogVisible = false;
      this.$refs["tableForm"].resetFields();
    },

    // 点击closeDetailTableDetail取消按钮
    closeDetailTableDetail() {
      this.dialogTableDetailVisible = false;
    },

    // 桌台二维码
    makeQRCode() {
      //创建二维码——此方法不能再当前组件的mounted中调用，因为绘图必须得canvas在el-dialog中，对话框在组件加载时并不在DOM树上
      var qrcode = require("qrcode");
      var canvas = document.getElementById("qrcanvas");
      var tableUrl =
        this.$store.state.globalSettings.apiUrl +
        "/#/" +
        this.tableChildList.tid;
      // 把绘制得到的图片二维码数据转为Base64编码的字符串
      qrcode.toDataURL(
        tableUrl,
        { errorCorrectionLevel: "H", width: 300 },
        (err, url) => {
          this.qrcodeData = url;
        }
      );
    }
  },
  props: ["data"]
};
</script>
<style lang="scss">
.xfn-table-info {
  padding: 3px;
  text-align: center;
  .xfn-table {
    width: 90%;
    height: 100px;
    line-height: 100px;
    border-radius: 50%;
    box-shadow: 5px -5px 5px #888;
    margin: 0 auto 8px;
  }
  .el-dialog {
    .el-dialog__header {
      text-align: left;
    }
    .el-form-item__content {
      text-align: left;
      margin-left: 100px;
    }
  }
  .el-radio {
    margin-right: 0;
  }
}
</style>


