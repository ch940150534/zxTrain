<template>
  <div id="managerHeader">
    {{title}}
  </div>
</template>

<script>
  export default {
    name: 'managerHeader',
    data () {
      return {
        title: ''
      }
    },
    mounted () {
      this.setTitle()
    },
    methods: {
      setTitle () {
        let {name} = this.$route
        switch (name) {
          case 'ForgetPsw':
            this.title = '忘记密码'
            break
          case 'Register':
            this.title = '注册信息'
            break
          case 'MemberManager':
            this.title = '员工管理'
            break
          case 'OrderManager':
            this.title = '账户充值'
            break
          case 'ScoreHistory':
            this.title = `成绩历史 : ${this.$route.params.name}`
            break
          case 'ScoreCount':
            this.title = '成绩统计'
            break
          case 'ChangePwd':
            this.title = '修改密码'
            break
          default:
            break
        }
      }
    },
    watch: {
      $route () {
        this.setTitle()
      }
    }
  }
</script>

<style lang="scss" scoped>
  #managerHeader {
    background-color: #e5e5e5;
    height: 55px;
    line-height: 55px;
    padding-left: 45px;
    font-size: 23px;
    font-weight: bold;
  }
</style>
