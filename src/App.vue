<template>
  <div id="app">
    <span id="title">
      <!--标题-->
      <img id="school_logo" src="http://check7.cnki.net/co_pic/neau.png" alt="" width="30" height="30">
      <strong >大学生毕业设计选题系统</strong>
    </span>
      <!--登陆窗口-->
    <span id="sign_info">
      <!-- 判断是否已经登陆 如果登陆，则显示用户名，否则显示登陆按钮 -->
      <span v-if="!isLogin">
        <button class="sign_in" v-on:click="login">登录</button>
        <button class="sign_in" v-on:click="regist">注册</button>
      </span>
      <span v-else style="margin-top: 15px">
        {{ loginUser }}
        <button class="sign_in" v-on:click="sign_out">注销</button>
      </span>
      <login @loginClose="loginClose" :loginShow="loginShow"></login>
      <regist @registClose="registClose" :registShow="registShow"></regist>
    </span>
      <!--导航-->
    <guide @gruidTab="changeTab" :tab="tab_pos" :login-user="loginUser"></guide>
      <!-- 正文 -->
    <span v-if="tab_pos === 'topic'">
      <!-- 课题列表 -->
        <topic-list :login-user="loginUser" :user-type=-1></topic-list>
    </span>
    <span v-else-if="tab_pos === 'profile'">
      <!-- 用户中心 -->
      <profile :login-user="loginUser" :user-type="UserType"></profile>
    </span>
    <router-view></router-view>
  </div>
</template>

<script>
import hello from './components/HelloWorld'
import topicList from './components/topic_list'
import guide from './components/guide'
import login from './components/login'
import regist from './components/Regist'
import Profile from './components/Profile'
import axios from 'axios'
export default {
  name: 'app',
  components: {
    Profile,
    hello,
    topicList,
    guide,
    login,
    regist
  },
  data () {
    return {
      loginUser: '',
      UserType: -1,
      isLogin: false,
      loginShow: false,
      registShow: false,
      tab_pos: 'topic'
    }
  },
  methods: {
    loadUserType: function () {
      console.log(this.loginUser)
      if (this.loginUser !== '') {
        let that = this
        axios.get('/api/user_type', { params: {
          login_user: this.loginUser
        }}).then(function (res) {
          if (res.data['status_code'] === 200) {
            that.UserType = res.data['data']
          }
        }, function () {
          console.log('get topic_info faild')
        })
      }
    },
    sign_out: function () {
      this.loginUser = ''
      this.isLogin = false
      this.tab_pos = 'topic'
      this.userType = -1
    },
    login: function () {
      this.loginShow = true
    },
    loginClose: function (data) {
      this.loginShow = false
      if (data !== '') {
        this.loginUser = data
        this.isLogin = true
        this.loadUserType()
      }
    },
    regist: function () {
      this.registShow = true
    },
    registClose: function (item) {
      this.registShow = false
      this.loginClose(item)
    },
    changeTab: function (item) {
      this.tab_pos = item
    }
  }
}
</script>

<style>
  #app {
    background-image:url(./assets/576df1167c887_1024.jpg);
    text-align: center;
  }
  #sign_info{
    color: #ff695f;
    font-size:20px;
    float: right;
    margin-top: 10px;
    margin-right: 30px;
  }
  #sign_info .sign_in{
    color: #ff695f;
    font-size:20px;
    margin-top: 10px;
  }
  #title{
    text-align: center;
    position: center;
    font-size: 40px;
    color: blue;
    margin-left: 10%;
  }
</style>
