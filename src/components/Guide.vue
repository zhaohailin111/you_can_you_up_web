<template>
  <div id="guide">
    <nav v-bind:class="tab" v-on:click.prevent>
      <a href="#" class="topic" v-on:click="makeActive('topic')">课题详情</a>
      <a href="#" class="profile" v-on:click="makeActive('profile')">个人中心</a>
    </nav>
  </div>
</template>

<script>
export default {
  name: 'guide',
  props: {
    tab: {
      default: 'topic'
    },
    loginUser: {
      default: ''
    }
  },
  data () {
    return {
      active: 'topic'
    }
  },
  methods: {
    changeTab (data) {
      this.$emit('gruidTab', data)
    },
    makeActive: function (item) {
      if (item === 'profile' && this.loginUser === '') {
        alert('请先登陆')
      } else {
        this.active = item
        this.$emit('guide_resp', item)
        this.changeTab(item)
      }
    }
  }
}
</script>

<style>
  #guide {
    height: 60px;
    background-color: #5daae3;
  }
  #guide a {
    display:inline-block;
    padding: 10px 30px;
    font-weight:bold;
    font-size: 40px;
    width: 20%;
    color:#fff !important;
    text-decoration:none !important;
    line-height:1;
  }
  nav.topic .topic,
  nav.profile .profile{
    background-color: #e3090f;
  }
</style>
