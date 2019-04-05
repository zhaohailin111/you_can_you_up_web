<template>
  <div class="login">
    <div class="dialog-cover back" v-if="loginShow"></div>
    <transition name="drop">
      <div class="dialog-content" v-if="loginShow">
        <div class="loginput">
          <input type="text" v-model="userName" placeholder="请输入用户名" /><br>
          <input type="password" v-model="passWord" placeholder="请输入密码" />
        </div>
        <div class="foot_close">
          <button class="close_img back" @click="login">确认</button>
          <button class="close_img back" @click="closeMyself('')">取消</button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'login',
  methods: {
    closeMyself (data) {
      this.$emit('loginClose', data)
    },
    login () {
      if (this.userName === '') {
        alert('用户名不能为空！')
      } else if (this.passWord.length < 6) {
        alert('密码长度必须大于6位！')
      } else {
        let that = this
        axios.post('/api/login', {
          userName: this.userName,
          passWord: this.passWord
        }).then(function (res) {
          alert(res.data['data'])
          if (res.data['status_code'] === 200) {
            that.closeMyself(that.userName)
            that.userName = ''
            that.passWord = ''
            that.confirm = ''
          }
        }, function () {
          alert('失败')
        })
      }
    }
  },
  data () {
    return {
      userName: '',
      passWord: ''
    }
  },
  props: {
    loginShow: {
      type: Boolean,
      default: false,
      required: true
    }
  }
}
</script>

<style scoped>
  .login {
    position: relative;
    color: #2e2c2d;
    font-size: 16px;
  }
  .dialog-cover {
    background: rgba(0,0,0, 0.8);
    position: fixed;
    z-index: 100;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  .dialog-content{
    background-color: aliceblue;
    border: 10px solid rgba(182, 144, 34, 0.84);
    position: fixed;
    align-items: center;
    z-index: 500;
    top:20%;
    width: 16%;
    left: 42%;
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  .loginput{
    padding-top: 5px;
    padding-bottom: 5px;
  }
  .foot_close{
    padding-bottom: 5px;
  }
</style>
