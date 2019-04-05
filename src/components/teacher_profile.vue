<template>
  <div id="teacher_profile">
    <div id="teacher_base">
      <div v-if="teacher_info_show === 'display'">
        <strong>我的信息</strong>
        <table id="teacher_base_table">
          <tr v-for='(item, key) in teacher_base' :key='key'>
            <td class="teacher_base_index">{{ item[0] }}</td>
            <td class="teacher_base_message" align="left">{{ item[1] }}</td>
          </tr>
        </table>
        <button class="sign_in" v-on:click="teacher_info_show_change('update')">修改</button>
      </div>
      <div v-else-if="teacher_info_show === 'update'">
        <strong>修改信息</strong>
        <table id="teacher_update_table">
          <tr>
            <td class="teacher_base_index">用户名:</td>
            <td class="teacher_base_message" align="left">{{loginUser}}</td>
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
            <td class="student_base_index">教工号:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input5" placeholder="请输入教工号"/></td>
          </tr>
          <tr>
            <td class="student_base_index">学 院:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input6" placeholder="请输入学院"/></td>
          </tr>
          <tr>
            <td class="student_base_index">职 称:</td>
            <td class="student_base_message" align="left">
              <input class="student_info_input" type="text" v-model="input7" placeholder="请输入职称"/></td>
          </tr>
        </table>
        <button class="sign_in" v-on:click="update_teacher_info()">保存</button>
      </div>
    </div>
    <div id="teacher_topic">
      <strong>我的课题</strong>
      <button class="info_button" @click="newTopic">发布新课题</button>
      <div id="teacher_topic_list" v-if="hahaha < 10" v-for='(item, key) in inforList' :key='key'>
        <div id="teacher_every_topic">
          <span>{{item[0]}}</span>
          <span>
            <button class="info_button" v-on:click="topic_show(item[item.length - 1], key)">
              <span v-if="topic_info_show[key]">
                收起
              </span>
              <span v-else>
                展开
              </span>
            </button>
          </span>
          <div id="info_show" v-if="topic_info_show[key]">
            <table id="topic_info_table">
            <tr v-for='(item, key) in topic_info' :key='key'>
              <td id="head">{{ item[0] }}</td>
              <td id="info">{{ item[1] }}</td>
            </tr>
            </table>
            <div id="table_buttion">
              <button style="font-size: 20px" @click="updateTopic(item[item.length - 1])">修改信息</button>
              <button style="font-size: 20px" @click="setGrade(item[item.length - 1])">打分</button>
              <button style="font-size: 20px" @click="deleteTopic(item[item.length - 1])">删除</button>
            </div>
          </div>
        </div>
      </div>
      <page :total="total_topic" @pagechange="pagechange"></page>
    </div>
    <NewOrUpdateTopic @newOrUpdateTopicClose="newOrUpdateTopicClose" :login-user="loginUser" :new-or-update-topic-show="NewOrUpdateTopicShow" :topic-id="updateTopicId"></NewOrUpdateTopic>
  </div>
</template>

<script>
import axios from 'axios'
import topicList from './topic_list'
import topicInfo from './topic_info'
import page from './page'
import NewOrUpdateTopic from './new_or_update_topic'
export default {
  name: 'teacher_profile.vue',
  components: {
    NewOrUpdateTopic,
    topicList,
    topicInfo,
    page
  },
  props: {
    loginUser: {
      default: ''
    }
  },
  data () {
    return {
      columnList: [],
      inforList: [],
      teacher_base: this.get_teacher_info(),
      topic_list: [],
      topic_info: [],
      topic_info_show: [false, false, false, false, false, false, false, false, false, false],
      teacher_info_show: 'display',
      input1: '',
      input2: '',
      input3: '',
      input4: '',
      input5: '',
      input6: '',
      input7: '',
      hahaha: 1,
      nowPage: 1,
      total_topic: 100,
      NewOrUpdateTopicShow: false,
      updateTopicId: 0,
      shouldUpdateView: false
    }
  },
  methods: {
    get_teacher_info () {
      let that = this
      axios.get('/api/teacher_info', { params: {
        login_user: this.loginUser
      }}).then(function (res) {
        if (res.data['status_code'] === 200) {
          console.log(res.data['data'])
          that.teacher_base = res.data['data']
        }
      }, function () {
        console.log('get topic_info faild')
      })
      this.get_topic_list(1)
      return that.teacher_base
    },
    pagechange: function (currentPage) {
      this.nowPage = currentPage
      this.get_topic_list(currentPage)
      this.topic_info_show = [false, false, false, false, false, false, false, false, false, false]
    },
    teacher_info_show_change () {
      this.teacher_info_show = 'update'
      this.fix_input()
    },
    get_topic_list (currentPage) {
      let that = this
      axios.get('/api/teacher_topic', { params: {
        page: currentPage,
        login_user: this.loginUser
      }}).then(function (res) {
        if (res.data['status_code'] === 200) {
          that.total_topic = res.data['data']['total']
          that.inforList = res.data['data']['topics']
        }
      }, function () {
        console.log('get topic faild')
      })
    },
    update_teacher_info () {
      let that = this
      axios.put('/api/teacher_info', {
        login_user: this.loginUser,
        name: this.input1,
        sex: this.input2,
        age: this.input3,
        nation: this.input4,
        school_id: this.input5,
        collage: this.input6,
        title: this.input7
      }).then(function (res) {
        if (res.data['status_code'] === 200) {
          console.log(res.data['data'])
          that.teacher_base = res.data['data']
        }
      }, function () {
        console.log('get topic_info faild')
      })
      this.teacher_info_show = 'display'
    },
    fix_input () {
      if (this.teacher_base.length > 0) {
        this.input1 = this.teacher_base[1][1]
        this.input2 = this.teacher_base[2][1]
        this.input3 = this.teacher_base[3][1]
        this.input4 = this.teacher_base[4][1]
        this.input5 = this.teacher_base[5][1]
        this.input6 = this.teacher_base[6][1]
        this.input7 = this.teacher_base[7][1]
      }
    },
    topic_show (itemId, index) {
      this.hahaha = (this.hahaha + 1) % 6 + 2
      let tmp = this.topic_info_show
      tmp[index] = !tmp[index]
      this.topic_info_show = tmp
      this.get_topic_info(itemId)
    },
    get_topic_info (itemId) {
      let that = this
      axios.get('/api/topic_info', { params: {
        topic_id: itemId
      }}).then(function (res) {
        if (res.data['status_code'] === 200) {
          console.log(res.data['data'])
          that.topic_info = res.data['data']
        }
      }, function () {
        console.log('get topic_info faild')
      })
    },
    newOrUpdateTopicClose () {
      this.updateTopicId = 0
      this.NewOrUpdateTopicShow = false
      this.topic_info_show = [false, false, false, false, false, false, false, false, false, false]
      this.hahaha = (this.hahaha + 1) % 6 + 2
      this.get_topic_list(this.nowPage)
      // pass
    },
    newTopic () {
      this.updateTopicId = 0
      this.NewOrUpdateTopicShow = true
    },
    updateTopic (item) {
      this.updateTopicId = item
      this.NewOrUpdateTopicShow = true
    },
    deleteTopic (item) {
      if (confirm('确认删除?')) {
        let that = this
        axios.delete('/api/delete_topic', {
          data: {
            topic_id: parseInt(item)
          }
        }).then(function (res) {
          if (res.data['status_code'] === 200) {
            alert('删除成功')
            that.newOrUpdateTopicClose()
          }
        }, function () {
          console.log('get topic_info faild')
        })
      }
    },
    setGrade (item) {
      let grade = prompt('输入您给出的成绩', '')
      if (grade != null && grade !== '') {
        let that = this
        axios.put('/api/update_topic', {
          grade: parseInt(grade),
          topic_id: item
        }).then(function (res) {
          if (res.data['status_code'] === 200) {
            alert('打分成功')
            that.newOrUpdateTopicClose()
          } else {
            alert(res.data['error_info'])
          }
        }, function (res) {
          console.log('get topic_info faild')
        })
      }
    }
  },
  watch: {
  }
}
</script>

<style scoped>
  #teacher_profile{
    border: 10px solid rgba(182, 165, 147, 0.84);
    position: center;
  }
  #teacher_base{
    border: 0px solid rgba(182, 165, 147, 0.84);
    padding-top: 10px;
    width: 30%;
    height: 500px;
    float: left;
  }
  #teacher_base_table, #teacher_update_table{
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
  .teacher_base_message{
    width: 20%;
    align-items: center;
  }
  .teacher_base_index{
    width: 20%;
    margin: auto;
  }
  #teacher_base .sign_in{
    bottom: 5px;
    margin-top: 20px;
    color: #ff695f;
    font-size:20px;
    margin-right: 30px;
  }
  #teacher_topic{
    padding-top: 10px;
    border-left: 10px rgba(182, 165, 147, 0.84) solid;
    width: 69.2%;
    margin-left: auto;
    height: 650px;
    overflow: auto;
  }
  #teacher_topic .sign_in{
    bottom: 5px;
    margin-top: 20px;
    color: #ff695f;
    font-size:20px;
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
  #teacher_every_topic{
    width: 90%;
    font-size: 30px;
    background:  no-repeat 13px 13px;

    border-radius: 10px;
    box-shadow: 3px 2px 5px #3f3f3f inset;
    text-align: left;
    font-family: inherit;
    color: #24292d;
    font-weight: bold;
    outline: none;
    text-indent: 40px;
    margin-top: 20px;
    margin-left: 5%;
  }
  .info_button{
    margin-bottom: 5px;
    color: rgba(0, 0, 0, 0.99);
    font-size:20px;
    float: right;
    margin-top: 5px;
    margin-right: 5px;
  }
  #table_buttion{
    color: rgba(0, 0, 0, 0.99);
    font-size: 10px;
    text-align: center;
    line-height: normal;
    margin: auto;
  }
  #info_show{
    padding-bottom: 10px;
  }
</style>
