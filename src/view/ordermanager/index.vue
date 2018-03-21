<template>
  <div id="ordermanager">
    <package></package>
    <order-list></order-list>
  </div>
</template>

<script>
  import { EventBus } from '../../util/eventBus'
  import Package from './package'
  import Pay from '../../components/pay/index'
  import OrderList from './orderList'

  export default {
    components: {
      OrderList,
      Pay,
      Package },
    name: 'ordermanager',
    mounted () {
      EventBus.$emit('setHomeHeader', '账户充值')
    }
  }
</script>

<style>
</style>
