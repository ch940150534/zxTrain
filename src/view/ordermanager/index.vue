<template>
  <div id="ordermanager">
    <package></package>
    <order-list></order-list>
  </div>
</template>

<script>
  import Package from './package'
  import Pay from '../../components/pay/index'
  import OrderList from './orderList'

  export default {
    components: {
      OrderList,
      Pay,
      Package },
    name: 'ordermanager'
  }
</script>

<style>
</style>
