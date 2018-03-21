<template>
  <div id="member-list">
    <div class="title">已经分配账号列表</div>
    <el-form :inline="true" :model="search" class="tool-bar" @submit.native.prevent>
      <el-form-item>
        <el-input
          prefix-icon="el-icon-search"
          v-model="search.name"
          size="small"
          placeholder="请输入姓名或手机号码"
          @keyup.enter.native="_search()">
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" size="small" @click="_search()">搜索</el-button>
      </el-form-item>
      <el-form-item>
        <a :href="baseUrl+'/excel/exportExcel'" target="_blank">
          <el-button type="primary" size="small">导出列表</el-button>
        </a>
      </el-form-item>
    </el-form>
    <el-table
      :data="data.rows"
      border
      fit
      style="width: 100%"
      :header-cell-style="{textAlign:'center'}"
      :cell-style="{textAlign:'center'}">
      <el-table-column
        label="序号"
        type="index"
        width="50"></el-table-column>
      <el-table-column
        label="姓名"
        prop="staff_name"></el-table-column>
      <el-table-column
        label="手机号码"
        prop="phone"></el-table-column>
      <el-table-column
        label="成绩">
        <template slot-scope="scope">
          {{scope.row.tails.score||'/'}}
        </template>
      </el-table-column>
      <el-table-column
        label="操作">
        <template slot-scope="scope">
          <el-row>
            <el-col :span="12">
              <el-button type="primary" size="small" @click="resetPwd(scope.row.phone)">重置密码</el-button>
            </el-col>
            <el-col :span="12" v-if="scope.row.tails.score">
              <el-button type="primary" size="small" @click="pointDetail(scope.row.user_id)">成绩详情</el-button>
            </el-col>
          </el-row>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      background
      layout="total, prev, pager, next"
      :page-size="search.pagesize"
      :total="parseInt(data.records)"
      @current-change="pageChange"
      class="container-pagination">
    </el-pagination>
    <score-detail/>
  </div>
</template>

<script>
  import { EventBus } from '../../util/eventBus'
  import ScoreDetail from './scoreDetail'

  export default {
    components: { ScoreDetail },
    name: 'member-list',
    data () {
      return {
        search: {
          name: '',
          page: '',
          pagesize: 10
        },
        data: ''
      }
    },
    mounted () {
      this.load()
      EventBus.$on('Addmember', () => { this.load() })
    },
    methods: {
      load () {
        this.$api.post('/manager/personList', this.search,
          resj => {
            this.data = resj.data
          })
      },
      pageChange (page) {
        this.search.page = page
        this.load()
      },
      resetPwd (phone) {
        this.$api.post('/account/passwordreset', { phone },
          resj => {
            this.$message.success('密码重置成功，默认密码：12345678')
          })
      },
      pointDetail (id) {
        this.$api.get('/manager/countPersonNew', { id },
          resj => {
            EventBus.$emit('showDetail', resj.data)
          })
      },
      _search () {
        this.search.page = ''
        this.load()
      }
    }
  }
</script>

<style lang="scss" scoped>
  #member-list {
    .container-pagination {
      text-align: center;
      margin-top: 20px;
    }
    .tool-bar {
      float: right;
      margin-top: -52px;
    }
  }
</style>
