<template>
  <div id="student_profile">
    <div id="student_base">
      <div v-if="student_info_show === 'display'">
        <strong>我的信息</strong>
        <table id="student_base_table">
          <tr v-for='(item, key) in student_base' :key='key'>
            <td class="student_base_index">{{ item[0] }}</td>
            <td class="student_base_message" align="left">{{ item[1] }}</td>
          </tr>
        </table>
        <button class="sign_in" v-on:click="student_info_show_change('update')">修改</button>
      </div>
      <div v-else-if="student_info_show === 'update'">
        <strong>修改信息</strong>
        <table id="student_update_table">
          <tr>
            <td class="student_base_index">用户名:</td>
            <td class="student_base_message" align="left">{{loginUser}}</td>
          </tr>
          <tr>
            <td class="student_base_index">姓 名:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input1" placeholder="请输入姓名"/></td>
          </tr>
          <tr>
            <td class="student_base_index">性 别:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input2" placeholder="请输入性别"/></td>
          </tr>
          <tr>
            <td class="student_base_index">年 龄:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input3" placeholder="请输入年龄"/></td>
          </tr>
          <tr>
            <td class="student_base_index">民 族:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input4" placeholder="请输入民族"/></td>
          </tr>
          <tr>
            <td class="student_base_index">学 号:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input5" placeholder="请输入学号"/></td>
          </tr>
          <tr>
            <td class="student_base_index">学 院:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input6" placeholder="请输入学院"/></td>
          </tr>
          <tr>
            <td class="student_base_index">班 级:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input7" placeholder="请输入班级"/></td>
          </tr>
        </table>
        <button class="sign_in" v-on:click="update_student_info()">保存</button>
      </div>
    </div>
    <div id="student_topic">
      <strong>我的课题</strong>
      <div v-if="topic_info.length === 0" style="font-size: 30px; margin-top: 10px">您还未选题,请到【课题详情】选题</div>
      <table id="topic_info_table">
        <tr v-for='(item, key) in topic_info' :key='key'>
          <td id="head">{{ item[0] }}</td>
          <td id="info">{{ item[1] }}</td>
        </tr>
      </table>
      <button v-if="topic_info.length !== 0" class="sign_in" v-on:click="delete_my_topic()">退选</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'student_profile',
  components: {
  },
  props: {
    loginUser: {
      default: ''
    }
  },
  data () {
    return {
      student_base: this.get_student_info(),
      topic_info: [],
      student_info_show: 'display',
      input1: '',
      input2: '',
      input3: '',
      input4: '',
      input5: '',
      input6: '',
      input7: ''
    }
  },
  methods: {
    get_topic_info () {
      let that = this
      axios.get('/api/topic_info', { params: {
        login_user: this.loginUser
      }}).then(function (res) {
        if (res.data['status_code'] === 200) {
          console.log(res.data['data'])
          that.topic_info = res.data['data']
        }
      }, function () {
        console.log('get topic_info faild')
      })
    },
    student_info_show_change (item) {
      this.student_info_show = item
      this.fix_input()
    },
    get_student_info () {
      let that = this
      axios.get('/api/student_info', { params: {
        login_user: this.loginUser
      }}).then(function (res) {
        if (res.data['status_code'] === 200) {
          console.log(res.data['data'])
          that.student_base = res.data['data']
        }
      }, function () {
        console.log('get topic_info faild')
      })
      this.get_topic_info()
      return that.student_base
    },
    update_student_info () {
      let that = this
      axios.put('/api/student_info', {
        login_user: this.loginUser,
        name: this.input1,
        sex: this.input2,
        age: this.input3,
        nation: this.input4,
        school_id: this.input5,
        collage: this.input6,
        class: this.input7
      }).then(function (res) {
        if (res.data['status_code'] === 200) {
          console.log(res.data['data'])
          that.student_base = res.data['data']
        }
      }, function () {
        console.log('get topic_info faild')
      })
      this.student_info_show_change('display')
    },
    delete_my_topic () {
      if (confirm('确认退选？')) {
        let that = this
        axios.post('/api/delete_student_topic', {
          login_user: this.loginUser
        }).then(function (res) {
          if (res.data['status_code'] === 200) {
            console.log(res.data['data'])
            that.topic_info = []
            alert('成功')
          }
        }, function () {
          console.log('get topic_info faild')
        })
      }
    },
    fix_input () {
      if (this.student_base.length > 0) {
        this.input1 = this.student_base[1][1]
        this.input2 = this.student_base[2][1]
        this.input3 = this.student_base[3][1]
        this.input4 = this.student_base[4][1]
        this.input5 = this.student_base[5][1]
        this.input6 = this.student_base[6][1]
        this.input7 = this.student_base[7][1]
      }
    }
  },
  watch: {
    loginUser: function () {
      if (this.loginUser !== '') {
        this.get_student_info()
        this.get_topic_info()
      }
    }
  }
}
</script>

<style scoped>
#student_profile{
  border: 10px solid rgba(182, 165, 147, 0.84);
  position: center;
  height: 800px;
}
#student_base{
  border: 0px solid rgba(182, 165, 147, 0.84);
  padding-top: 10px;
  width: 30%;
  height: 500px;
  float: left;
}
#student_base_table, #student_update_table{
  width: 100%;
  font-size: 30px;
}
input {
  background:#fff no-repeat 10px 10px;
  padding: 5px 0;

  border-radius: 2px;
  box-shadow: 0 2px 8px #c4c4c4 inset;
  text-align: left;
  font-size: 14px;
  color: #738289;
  text-indent: 10px;
}
.student_base_message{
  width: 20%;
  align-items: center;
}
.student_base_index{
  width: 20%;
  margin: auto;
}
#student_base .sign_in{
  bottom: 5px;
  margin-top: 20px;
  color: #ff695f;
  font-size:20px;
  margin-right: 30px;
}
#student_topic{
  padding-top: 10px;
  border-left: 10px rgba(182, 165, 147, 0.84) solid;
  margin-left: 30%;
  width: 68%;
  height: 790px;
}
#student_topic .sign_in{
  bottom: 5px;
  margin-top: 20px;
  color: #ff695f;
  font-size:20px;
  margin-right: 30px;
  margin-bottom: 10px;
}
#topic_info_table {
  padding:1px;
  font-size:  30px;
  margin-left: 15%;
  width: 80%;
}
strong{
  font-size:  30px;
}
</style>
