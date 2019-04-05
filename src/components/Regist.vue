<template>
  <div class="login">
    <div class="dialog-cover back" v-if="registShow"></div>
    <transition name="drop">
      <div class="dialog-content" v-if="registShow">
        <div class="loginput">
          <input type="text" v-model="userName" placeholder="请输入用户名" ><br>
          <input type="password" v-model="passWord" placeholder="请输入密码" >
          <input type="password" v-model="confirm" placeholder="确认密码" >
          <div v-if="confirm !== '' && passWord !== confirm" id="confirm">两次输入密码不一致</div>
          <div id="rolebox">账号类型
            <input type="radio" id="rdoTea" v-model="type" checked="checked" value=1 /><label for="rdoTea">学生</label>
            <input type="radio" id="rdoStu" v-model="type" value=2 /><label for="rdoStu">教师</label>
          </div>

        </div>
        <div class="foot_close">
          <button class="close_img back" @click="registUser">注册并登录</button>
          <button class="close_img back" @click="closeMyself(false)">取消</button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'regist',
  methods: {
    closeMyself (item) {
      if (item) {
        this.$emit('registClose', this.userName)
      } else {
        this.$emit('registClose', '')
      }
      this.userName = ''
      this.passWord = ''
      this.confirm = ''
    },
    registUser () {
      if (this.userName === '') {
        alert('用户名不能为空！')
      } else if (this.passWord.length < 6) {
        alert('密码长度必须大于6位！')
      } else if (this.passWord !== this.confirm) {
        alert('两次输入的密码不一致！')
      } else {
        let that = this
        axios.post('/api/regist', {
          userName: this.userName,
          passWord: this.passWord,
          type: parseInt(this.type)
        }).then(function (res) {
          alert(res.data['data'])
          if (res.data['data'] === '注册成功') {
            that.closeMyself(true)
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
      passWord: '',
      confirm: '',
      type: 1
    }
  },
  props: {
    registShow: {
      type: Boolean,
      default: false,
      required: true
    }
  }
}
</script>

<style scoped>
  .dialog-cover {
    background: rgba(0,0,0, 0.8);
    position: fixed;
    z-index: 200;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  .dialog-content{
    background-color: aliceblue;
    border: 10px solid rgba(182, 144, 34, 0.84);
    position: fixed;
    top:20%;
    width: 16%;
    left: 42%;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    z-index: 300;
    color: #17121c;
  }
  .loginput{
    padding-top: 5px;
    padding-bottom: 5px;
  }
  .foot_close{
    padding-bottom: 5px;
  }
  #confirm{
    color: rgba(182,0,11,0.84);
    font-size: 1px;
  }
</style>
