<template>
  <el-table
    :data="data"
    border
    :header-cell-style="{textAlign:'center'}"
    :cell-style="{textAlign:'center'}"
    style="width: 100%"
  >
    <el-table-column label="序号" width="50" type="index"></el-table-column>
    <el-table-column label="题库类型" prop="tails.bank_name"></el-table-column>
    <el-table-column label="开始时间" prop="start_time"></el-table-column>
    <el-table-column label="结束时间" prop="end_time"></el-table-column>
    <el-table-column label="分数" prop="score"></el-table-column>
  </el-table>
</template>

<script>
  import { EventBus } from '../../util/eventBus'

  export default {
    name: 'scorehistory',
    data () {
      return {
        data: []
      }
    },
    mounted () {
      EventBus.$emit('setHomeHeader', `成绩历史 : ${this.$route.params.name}`)
      this.load()
    },
    methods: {
      load () {
        this.$api.get('/manager/countPerson', { id: this.$route.params.id },
          resj => {
            this.data = resj.data
          })
      }
    }
  }
</script>

<style scoped>

</style>
