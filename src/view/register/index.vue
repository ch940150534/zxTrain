<template>
  <div id="register" class="container">
    <manager-header/>
    <div class="registerForm">
      <el-form :model="registerForm" :rules="rules" ref="registerForm" label-width="100px">
        <el-form-item label="用户名" prop="account">
          <el-col :span="12">
            <el-input v-model="registerForm.account"></el-input>
          </el-col>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-col :span="12">
            <el-input v-model="registerForm.password" type="password"></el-input>
          </el-col>
        </el-form-item>
        <el-form-item label="手机号" prop="phone">
          <el-col :span="12">
            <el-input v-model="registerForm.phone"></el-input>
          </el-col>
        </el-form-item>
        <el-form-item label="验证码" prop="code">
          <el-col :span="12">
            <el-input v-model="registerForm.code">
              <el-button slot="append" @click="getCode" :class="codeBtnDisabled ? 'is-waiting' : ''">{{codeBtnText}}
              </el-button>
            </el-input>
          </el-col>
        </el-form-item>
        <el-form-item label="公司名称" prop="partner_name">
          <el-col :span="12">
            <el-input v-model="registerForm.partner_name"></el-input>
          </el-col>
        </el-form-item>
        <el-form-item label="姓名" prop="name">
          <el-col :span="12">
            <el-input v-model="registerForm.name"></el-input>
          </el-col>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-col :span="12">
            <el-input
              v-model="registerForm.email"
              @keyup.enter.native="submitForm('registerForm')"></el-input>
          </el-col>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('registerForm')">立即注册</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
  import ManagerHeader from '../../components/managerHeader'
  import { EventBus } from '../../util/eventBus'

  export default {
    name: 'Register',
    data () {
      const validatePhone = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入手机号'))
        } else {
          if (!(/^1[345789]\d{9}$/.test(value))) {
            callback(new Error('请输入正确的11位手机号'))
          }
          callback()
        }
      }
      const validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'))
        } else {
          if (!(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[^]{8,30}$/.test(value))) {
            callback(new Error('最少8个字符，需包含大小写字母及数字'))
          }
          callback()
        }
      }
      return {
        codeBtnDisabled: false,
        codeBtnText: '获取短信验证码',
        codeBtnTime: 90,
        registerForm: {
          account: '',
          password: '',
          partner_name: '',
          phone: '',
          code: '',
          name: '',
          email: ''
        },
        rules: {
          account: [
            { required: true, message: '请输入用户名', trigger: 'blur' },
            { min: 6, max: 20, message: '用户名长度为6到20个字符', trigger: 'blur' }],
          password: [{ required: true, validator: validatePass, trigger: 'blur' }],
          partner_name: [{ required: true, message: '请输入公司名称', trigger: 'blur' }],
          phone: [{ required: true, validator: validatePhone, trigger: 'blur' }],
          code: [{ required: true, message: '请输入手机验证码', trigger: 'blur' }],
          name: [{ required: true, message: '请输入姓名', trigger: 'blur' }],
          email: [{ required: true, type: 'email', message: '请输入电子邮箱', trigger: 'blur' }]
        }
      }
    },
    components: { ManagerHeader },
    mounted () {
      EventBus.$emit('setHomeHeader', '注册信息')
    },
    methods: {
      submitForm (formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            this.$api.post(
              '/manager/register',
              this.registerForm,
              resj => {
                this.$router.replace({ path: '/membermanager' })
              }
            )
          } else {
            console.log('提交失败')
            return false
          }
        })
      },
      getCode () {
        this.$refs.registerForm.validateField('phone', (errorPhone) => { // 电话号合法性判断
          if (!errorPhone) {
            this.$api.post(
              '/account/sendsms',
              { phone: this.registerForm.phone, account: this.registerForm.account },
              resj => {
                this.setCodeBtnTime()
                this.$message({
                  message: resj.message,
                  type: 'success'
                })
              }
            )
          } else {
            console.log(errorPhone)
          }
        })
      },
      setCodeBtnTime () {
        if (!this.codeBtnTime) { // 0秒时重置
          this.codeBtnDisabled = false
          this.codeBtnText = '获取短信验证码'
          this.codeBtnTime = 90
          return 'reset time'
        } else {
          this.codeBtnDisabled = true
          this.codeBtnText = `重新发送(${this.codeBtnTime})`
          this.codeBtnTime--
        }
        setTimeout(() => {
          this.setCodeBtnTime()
        }, 1000)
      }
    }
  }
</script>
<style lang="scss">
  #register {
    padding: 40px 0;
    background-color: #fff;
    box-shadow: 0 0 10px #ccc;
    .el-input-group__append button.is-waiting {
      pointer-events: none;
    }
    .el-input-group__append button.is-waiting:before {
      pointer-events: none;
      background-color: rgba(255, 255, 255, .35);
    }
    .registerForm {
      width: 720px;
      margin: 50px auto 0;
      .requier {
        color: red;
        font-family: 'simsun';
        text-align: center;
      }
      .tip {
        color: #929292;
      }
      .link-protocol {
        color: $hot-dark;
      }
      .link-protocol:hover {
        color: #d0425d;
      }
    }
  }
</style>
