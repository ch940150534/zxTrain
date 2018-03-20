<template>
  <div id="membermanager">
    <member-add></member-add>
    <member-list></member-list>
  </div>
</template>

<script>
  import { EventBus } from '../../util/eventBus'
  import MemberList from './memberList'
  import MemberAdd from './memberAdd'

  export default {
    components: {
      MemberAdd,
      MemberList },
    name: 'membermanager',
    data () {
      return {}
    },
    mounted () {
      EventBus.$emit('setHomeHeader', '员工管理')
    }
  }
</script>

<style lang="scss">
  #membermanager{
    .title{
      border-bottom: 1px solid #ddd;
      padding: 10px;
      margin-bottom: 10px;
      font-size: 20px;
    }
  }
</style>
