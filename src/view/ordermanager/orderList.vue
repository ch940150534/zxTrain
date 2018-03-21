<template>
  <div class="order-list">
    <el-table
      :data="data"
      border
      :cell-style="{textAlign:'center'}"
      :header-cell-style="{textAlign:'center'}"
      style="width: 100%">
      <el-table-column label="序号" type="index" width="50"></el-table-column>
      <el-table-column label="日期" prop="create_time" :formatter="formatDate"></el-table-column>
      <el-table-column label="金额" prop="amount" :formatter="formatAmount"></el-table-column>
      <el-table-column label="订单编号" prop="id"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <span style="color: #67c23a;" v-if="scope.row.pay_state === '已付款'">已付款</span>
          <span v-else-if="scope.row.channel === 'remittance'">银行汇款审批中</span>
          <a @click.prevent="pay(scope.row.id)" class="btnPay" v-else>继续支付</a>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      background
      layout="total, prev, pager, next"
      :total="parseInt(pagination.total)"
      :page-size="pagination.pagesize"
      @current-change="changePage"
      class="paginationContainer"
    ></el-pagination>
  </div>
</template>

<script>
  import { EventBus } from '../../util/eventBus'

  export default {
    name: 'order-list',
    data () {
      return {
        pagination: {
          page: '',
          pagesize: 10,
          total: ''
        },
        data: []
      }
    },
    mounted () {
      EventBus.$on('orderListLoad', () => {
        this.load()
      })
      this.load()
    },
    methods: {
      load () {
        this.$api.get('/zxOrder/view', this.pagination,
          resj => {
            this.data = resj.data.rows
            this.pagination.total = resj.data.records
          })
      },
      formatAmount (row, col, val) {
        return val / 100
      },
      formatDate (row, col, val) {
        let date = new Date(val * 1000)
        let year = date.getFullYear()
        let month = date.getMonth() + 1
        let day = date.getDate()
        return `${year}/${month}/${day}`
      },
      pay (id) {
        EventBus.$emit('continuePay', id)
      },
      changePage (page) {
        this.pagination.page = page
        this.load()
      }
    }
  }
</script>

<style lang="scss" scoped>
  .btnPay {
    color: $hot-dark;
    &:hover {
      color: $hot-light;
    }
  }

  .paginationContainer {
    text-align: center;
    margin-top: 20px;
  }
</style>
