<template>
  <div id="member-add">
    <div class="title">账号新增</div>
    <div class="body">
      <el-form :data="form" :inline="true">
        <el-form-item label="姓名" prop="staff_name">
          <el-input v-model="form.staff_name"></el-input>
        </el-form-item>
        <el-form-item label="手机号码" prop="phone">
          <el-input v-model="form.phone"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="add()">新增</el-button>
        </el-form-item>
      </el-form>
      <p class="tips">新增账号后，员工的手机号码将作为登录用户名，默认密码为12345678（重置密码也是此密码）</p>
      <p class="tips">您在中消在线余额为<span class="num">{{recharge}}</span>元，每<span class="num">{{price}}</span>元可为下属员工开通一个培训账户，您目前可分配<span class="num">{{recharge/price||0}}</span>个账户
        <router-link to="/home/ordermanager">
          <el-button type="primary" size="small">续费</el-button>
        </router-link>
      </p>
    </div>
  </div>
</template>

<script>
  import { EventBus } from '../../util/eventBus'

  export default {
    name: 'member-add',
    data () {
      return {
        form: {
          staff_name: '',
          phone: ''
        },
        recharge: 0,
        price: 0
      }
    },
    mounted () {
      this.load()
    },
    methods: {
      add () {
        this.$api.post('/manager/add', this.form,
          resj => {
            EventBus.$emit('Addmember')
            this.load()
          }
        )
      },
      load () {
        this.$api.get('/account/getinfo', null,
          resj => {
            if (resj.data.tails.recharge) {
              this.recharge = resj.data.tails.recharge.now_balance / 100
            }
            this.price = resj.data.tails.price
          })
      }
    }
  }
</script>

<style lang="scss" scoped>
  #member-add {
    .body {
      padding: 10px 0;
      padding-left: 60px;
    }
    .tips:before {
      content: '*';
      font-family: 'simsun';
      margin-right: 5px;
      color: red
    }
    .num {
      color: red;
    }
  }
</style>
