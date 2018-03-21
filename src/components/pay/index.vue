<template>
  <div id="pay">
    <el-dialog title="付款方式" :visible.sync="dialogShow" width="600px" id="pay-dialog" @close="closeDialog">
      <h2 class="text-center">友情提示</h2>
      <template v-if="payType==='remittance'">
        <p>
          *教育培训的充值费最低<span class="text-hot">200</span>元/年,我们将在收到款项之日起,为您完成余额充值。
          使用对公付款方式时，请在转账备注中写明<span class="text-hot">“教育培训充值费”</span>。
        </p>
        <p>
          *由于银行间转账并非实时到账，请提前安排。我们将在收到款项后，用短信和邮件的方式提醒您。届时可在<span class="text-green">“员工管理”</span>页面进行账号分配。
        </p>
        <p>
          <b>公司名称：</b><span>上海中消网络科技有限公司</span>
        </p>
        <p>
          <b>开户银行：</b><span>交通银行上海第一支行</span>
        </p>
        <p>
          <b>银行账号：</b><span>310066726018800051881</span>
        </p>
        <p>
          <b>订单编号：</b><span>{{payInfo.id}}</span>
        </p>
      </template>
      <template v-else>
        <p>
          *教育培训的充值费最低<span class="text-hot">200</span>元/年,我们将在收到款项之日起,为您完成余额充值。
        </p>
        <p>
          *如您目前决定稍后付款，可登录后前往 <span class="text-green">“会员充值”</span>页面缴费。
        </p>
      </template>
      <h3 class="text-hot">选择支付方式付款</h3>
      <el-row v-model="payType">
        <el-col :span="8">
          <el-radio v-model="payType" label="alipay_pc_direct">
            <img src="./images/alipay.jpg" alt="">
          </el-radio>
        </el-col>
        <el-col :span="8">
          <el-radio v-model="payType" label="wx_pub_qr">
            <img src="./images/wx.jpg" alt="" class="img-responsive">
          </el-radio>
        </el-col>
        <el-col :span="8">
          <el-radio v-model="payType" label="remittance">
            <img src="./images/bank.jpg" alt="" class="img-responsive">
          </el-radio>
        </el-col>
      </el-row>
      <el-collapse-transition>
        <div class="qrCode" v-show="qrCode&&payType==='wx_pub_qr'">
          <vue-qrcode :value="qrCode" :options="{ size: 200 }"></vue-qrcode>
        </div>
      </el-collapse-transition>
      <div class="text-center button-container">
        <el-button type="primary" v-if="payType=='alipay_pc_direct'" @click="aliPay">立即付款
        </el-button>
        <el-button type="primary" v-if="payType=='remittance'" @click="remittancePay">汇款成功</el-button>
        <el-button type="primary" @click="dialogShow = false">稍后付款</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import pingpp from 'pingpp-js'
  import VueQrcode from '@xkeshi/vue-qrcode'
  import { EventBus } from '../../util/eventBus'

  export default {
    components: {
      VueQrcode
    },
    name: 'pay',
    data () {
      return {
        payId: '',
        dialogShow: false,
        payInfoUrl: '/item/order/',
        remittanceUrl: '/zxOrder/update/',
        payUrl: '/pay/',
        payStatusUrl: '/pay/status/',
        payStatus: '',
        payInfo: {
          id: ''
        },
        payType: '',
        qrCode: ''
      }
    },
    mounted () {
      EventBus.$on('pay', id => {
        this.dialogShow = true
        this.payId = id
        this.getPayInfo()
      })
      EventBus.$on('continuePay', id => {
        this.dialogShow = true
        this.payInfo.id = id
      })
    },
    methods: {
      // 发起订单
      getPayInfo () {
        this.$api.post(this.payInfoUrl + this.payId, null,
          resj => {
            this.payInfo = resj.data
            this.dialogShow = true
          })
      },
      // 银行线下汇款
      remittancePay () {
        this.$api.post(this.remittanceUrl + this.payInfo.id, null,
          resj => {
            this.dialogShow = false
          })
      },
      // ping++支付
      aliPay () {
        this.$api.post(`${this.payUrl}${this.payType}/${this.payInfo.id}`, null,
          resj => {
            if (this.payType === 'wx_pub_qr') {
              this.qrCode = resj.data.credential.wx_pub_qr
            } else {
              pingpp.setUrlReturnCallback((err, url) => {
                if (err) {
                  this.$message.error(err)
                } else {
                  window.open(url)
                }
              }, ['alipay_pc_direct'])
              pingpp.createPayment(resj.data, (result, err) => {
                console.log(result)
                console.log(err.msg)
                console.log(err.extra)
                if (result === 'success') {
                  // 只有微信公众账号 wx_pub 支付成功的结果会在这里返回，其他的支付结果都会跳转到 extra 中对应的 URL。
                } else if (result === 'fail') {
                  // charge 不正确或者微信公众账号支付失败时会在此处返回
                } else if (result === 'cancel') {
                  // 微信公众账号支付取消支付
                }
              })
            }
            this.pay_status()
          }
        )
      },
      // 支付状态轮询
      pay_status () {
        this.$api.post(this.payStatusUrl + this.payInfo.id, null,
          resj => {
            if (resj.data.pay_state === '未付款') {
              this.payStatus = setTimeout(() => {
                this.pay_status()
              }, 2000)
            } else {
              this.dialogShow = false
            }
          })
      },
      // 模态框关闭
      closeDialog () {
        EventBus.$emit('orderListLoad')
        clearTimeout(this.payStatus)
      }
    },
    watch: {
      payType (value) {
        if (value === 'wx_pub_qr') {
          this.aliPay()
        }
      }
    }
  }
</script>

<style lang="scss">
  #pay-dialog {
    text-align: left;
    .el-dialog__body {
      padding: 20px 60px;
    }
    p {
      text-align: left;
    }
    .text-hot {
      color: #c51234;
    }
    .text-green {
      color: #089b01
    }
    h3.text-hot {
      font-size: 14px;
      font-weight: bold;
      margin-bottom: 10px;
    }
    .el-radio {
      margin-bottom: 20px;
      img {
        width: 95px;
        height: 34px;
        margin-bottom: -13px;
        display: inline-block;
      }
    }
    .qrCode {
      height: 200px;
      margin-bottom: 20px;
      text-align: center;
    }
  }
</style>
