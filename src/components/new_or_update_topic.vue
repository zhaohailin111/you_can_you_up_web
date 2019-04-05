<template>
  <div id="new_or_update_topic">
    <div class="dialog-cover back" v-if="NewOrUpdateTopicShow"></div>
      <div class="dialog-content" v-if="NewOrUpdateTopicShow">
        <div class="loginput">
          <textarea class="topic_input" type="text" v-model="topicName" placeholder="请输入课题名称" style="height: "></textarea><br>
          <textarea class="topic_input" type="text" v-model="topicRequest" placeholder="请输入课题要求" ></textarea>
          <textarea class="topic_input" type="text" v-model="topicInfo" placeholder="请输入课题详情" ></textarea>
        </div>
        <div class="foot_close">
          <button class="close_img back" @click="saveOrUpdate">确定</button>
          <button class="close_img back" @click="closeMyself">取消</button>
        </div>
      </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'new_or_update_topic',
  props: {
    NewOrUpdateTopicShow: {
      default: false
    },
    topicId: {
      default: 0
    },
    loginUser: {
      default: ''
    }
  },
  data () {
    return {
      topicName: '',
      topicRequest: '',
      topicInfo: ''
    }
  },
  methods: {
    closeMyself () {
      this.topicName = ''
      this.topicRequest = ''
      this.topicInfo = ''
      this.topicId = 0
      this.$emit('newOrUpdateTopicClose', '')
    },
    saveOrUpdate () {
      if (this.topicName === '') {
        alert('课题名称不能为空')
      } else if (this.topicId <= 0) {
        let that = this
        axios.post('/api/add_topic', {
          login_user: this.loginUser,
          topic_name: this.topicName,
          topic_request: this.topicRequest,
          topic_info: this.topicInfo,
          topic_id: this.topicId
        }).then(function (res) {
          if (res.data['status_code'] === 200) {
            alert('创建成功')
            that.closeMyself()
          } else {
            alert(res.data['error_info'])
          }
        }, function () {
          console.log('get topic_info faild')
        })
      } else {
        let that = this
        axios.put('/api/update_topic', {
          login_user: this.login_user,
          topic_name: this.topicName,
          topic_request: this.topicRequest,
          topic_info: this.topicInfo,
          topic_id: this.topicId
        }).then(function (res) {
          if (res.data['status_code'] === 200) {
            alert('更新成功')
            that.closeMyself()
          }
        }, function () {
          console.log('get topic_info faild')
        })
      }
    },
    getTopicInfo () {
      if (this.topicId !== 0) {
        let that = this
        axios.get('/api/topic_info', {
          params: {
            topic_id: this.topicId
          }
        }).then(function (res) {
          if (res.data['status_code'] === 200) {
            console.log(res.data['data'])
            that.topicName = res.data['data'][0][1]
            that.topicRequest = res.data['data'][2][1]
            that.topicInfo = res.data['data'][3][1]
          }
        }, function () {
          console.log('get topic_info faild')
        })
      }
    }
  },
  watch: {
    topicId () {
      this.getTopicInfo()
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
    width: 40%;
    left: 30%;
    min-height: 10px;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    z-index: 300;
    color: #17121c;
  }
  .loginput{
    padding-top: 5px;
    padding-bottom: 5px;
    margin: auto;
  }
  .foot_close{
    padding-bottom: 5px;
  }
  .topic_input{
    width: 80%;
    height: 50px;
    line-height: 10px;
    margin-top: 5px;
    margin-bottom: 5px;
    font-size: 15px;
  }
</style>
