<template>
  <div id="scorecount">
    <el-table
      :data="data"
      border
      :header-cell-style="{textAlign:'center'}"
      :cell-style="{textAlign:'center'}"
      style="width: 100%">
      <el-table-column
        label="序号"
        type="index"
        width="50"></el-table-column>
      <el-table-column
        label="员工姓名">
        <template slot-scope="scope">
          <router-link :to="'/home/scorehistory/'+scope.row.user_id+'/'+scope.row.staff_name">
            {{scope.row.staff_name}}
          </router-link>
        </template>
      </el-table-column>
      <el-table-column
        label="社会消防科普"
        prop="5"
        :formatter="ifNull"></el-table-column>
      <el-table-column
        label="保洁/绿化"
        prop="2"
        :formatter="ifNull"></el-table-column>
      <el-table-column
        label="秩序/客服"
        prop="1"
        :formatter="ifNull"></el-table-column>
      <el-table-column
        label="消控室人员"
        prop="3"
        :formatter="ifNull"></el-table-column>
      <el-table-column
        label="管理层人员"
        prop="4"
        :formatter="ifNull"></el-table-column>
    </el-table>
    <el-pagination
      background
      layout="total, prev, pager, next"
      :total="parseInt(pagination.total)"
      :page-size="parseInt(pagination.pagesize)"
      @current-change="pageChange"
      class="container-pagination"></el-pagination>
  </div>
</template>

<script>
  export default {
    name: 'scorecount',
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
      this.load()
    },
    methods: {
      load () {
        this.$api.get('/manager/countList', this.pagination,
          resj => {
            // let array = []
            let personList = resj.data.partnerstafflist
            personList.rows.map((val, index) => {
              let person = val
              if (resj.data[person.id]) {
                let scoreList = resj.data[person.id] // 获取人员对应成绩记录
                scoreList.map((val, index) => {
                  person[val.bank_id] = val.score // 对应题库成绩按题库id插入人员列表
                })
              }
            })
            this.data = personList.rows
            this.pagination.total = personList.records
          })
      },
      ifNull (row, col, val) {
        return val || '-'
      },
      pageChange (page) {
        this.pagination.page = page
        this.load()
      }
    }
  }
</script>

<style scoped>
  .container-pagination {
    text-align: center;
    margin-top: 20px;
  }
</style>
