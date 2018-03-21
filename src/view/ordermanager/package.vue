<template>
  <div id="package">
    <p class="tips">您在中消在线余额为<span class="num">{{recharge}}</span>元，每<span class="num">{{price}}</span>元可为下属员工开通一个培训账户，您目前可分配<span
      class="num">{{recharge/price||0}}</span>个账户
    </p>
    <el-row :gutter="40">
      <el-col v-for="(item,index) in data" :span="6" :key="index">
        <div class="item" @click="choose(item.id)" :style="active===item.id?{borderColor: 'red'}:null">
          <div class="price">{{item.price}}元</div>
          <div class="description">{{item.description}}</div>
        </div>
      </el-col>
    </el-row>
    <div class="text-center">
      <el-button type="primary" @click="pay">
        付款
      </el-button>
    </div>
    <pay></pay>
  </div>
</template>

<script>
  import { EventBus } from '../../util/eventBus'
  import Pay from '../../components/pay/index'

  export default {
    components: { Pay },
    name: 'package',
    data () {
      return {
        data: '',
        recharge: 0,
        price: 0,
        active: null
      }
    },
    mounted () {
      this.load()
    },
    methods: {
      load () {
        this.$api.get('/item/publiclist', null,
          resj => {
            this.data = resj.data
          })
        this.$api.get('/account/getinfo', null,
          resj => {
            if (resj.data.tails.recharge) {
              this.recharge = resj.data.tails.recharge.now_balance / 100
            }
            this.price = resj.data.tails.price
          })
      },
      pay () {
        EventBus.$emit('pay', this.active)
      },
      choose (id) {
        this.active = id
      }
    }
  }
</script>

<style lang="scss" scoped>
  #package {
    margin-bottom: 20px;
    .num {
      color: red
    }
    .tips:before {
      content: '*';
      font-family: 'simsun';
      margin-right: 5px;
      color: red
    }
    .item {
      border: 2px solid #ccc;
      margin-bottom: 15px;
      padding: 8px;
      text-align: center;
      cursor: pointer;
      transition: border-color .2s linear .2s;
      &:hover {
        border-color: red;
      }
      .price {
        font-weight: bold;
        font-size: 20px;
        margin-bottom: 5px;
      }
      .description {
      }
    }
  }
</style>
