<template>
  <el-dialog title="成绩详情" :visible.sync="show" width="580px">
    <div class="infoList" v-if="data">
      <div class="infoItem">
        <label>姓名：</label>
        <div class="infoContent">{{data.result[0].tails.staff_name}}</div>
      </div>
      <div class="infoItem">
        <label>最新成绩：</label>
        <div class="infoContent">{{data.result[0].score}}</div>
      </div>
      <div class="infoItem">
        <label>上一次成绩：</label>
        <div class="infoContent">{{data.result[1]?data.result[1].score:'-'}}</div>
      </div>
      <div class="infoItem">
        <label>答题次数：</label>
        <div class="infoContent">{{data.num.tails.num}}</div>
      </div>
      <div class="infoItem">
        <label>答题用时：</label>
        <div class="infoContent">{{intervals}}</div>
      </div>
    </div>
  </el-dialog>
</template>

<script>
  import { EventBus } from '../../util/eventBus'

  export default {
    name: 'score-detail',
    data () {
      return {
        show: false,
        data: ''
      }
    },
    mounted () {
      EventBus.$on('showDetail', (val) => {
        this.show = true
        this.data = val
      })
    },
    computed: {
      intervals () {
        const DAY = 24 * 3600 * 1000
        const HOURE = DAY / 24
        const MINUTE = HOURE / 60
        const SECOND = MINUTE / 60
        let start = new Date(this.data.result[0].start_time)    // 开始时间
        let end = new Date(this.data.result[0].end_time)    // 结束时间
        let intervals = end.getTime() - start.getTime() // 时间差秒
        // 计算出相差天数
        let days = Math.floor(intervals / DAY)
        // 计算出小时数
        let leave1 = intervals % (DAY)// 计算天数后剩余的毫秒数
        let hours = Math.floor(leave1 / HOURE)
        // 计算相差分钟数
        let leave2 = leave1 % HOURE   // 计算小时数后剩余的毫秒数
        let minutes = Math.floor(leave2 / MINUTE)
        // 计算相差秒数
        let leave3 = leave2 % (MINUTE)    // 计算分钟数后剩余的毫秒数
        let seconds = Math.round(leave3 / SECOND)
        return (days + ' 天 ' + hours + ' 时 ' + minutes + ' 分 ' + seconds + ' 秒 ')
      }
    }
  }
</script>

<style lang="scss" scoped>
  .infoList {
    width: 400px;
    margin: 0 auto;
    .infoItem {
      margin-bottom: 20px;
      $label-width: 100px;
      label {
        float: left;
        line-height: 36px;
        font-weight: bold;
        text-align: right;
        width: $label-width;
      }
      .infoContent {
        margin-left: $label-width;
        line-height: 34px;
        height: 36px;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-shadow: 2px 2px 2px #ccc;
        text-align: center;
      }
    }
  }
</style>
