<template>
  <div class="topic_info">
    <div class="dialog">
      <!--外层的遮罩 点击事件用来关闭弹窗，isShow控制弹窗显示 隐藏的props-->
      <div class="dialog-cover back" v-if="isShow" @click="closeMyself"></div>
      <!-- transition 这里可以加一些简单的动画效果 -->
      <transition name="drop">
        <!--style 通过props 控制内容的样式 -->
        <div class="dialog-content" v-if="isShow">
          <div class="dialog_head back">
            <!--弹窗头部 title-->
            <h1 id="header">课题详情</h1>
          </div>
          <div class="dialog_main">
            <!--弹窗的内容-->
            <table id="topic_info_table">
              <tr v-for='(item, key) in topic_info' :key='key'>
                <td id="head">{{ item[0] }}</td>
                <td id="info">{{ item[1] }}</td>
              </tr>
            </table>
          </div>
          <!--弹窗关闭按钮-->
          <div class="foot_close" @click="closeMyself">
            <button class="close_img back">确认</button>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'topic_info',
  methods: {
    closeMyself () {
      this.$emit('hidden', '')
    },
    get_topic_info () {
      let that = this
      axios.get('/api/topic_info', { params: {
        topic_id: this.topicId
      }}).then(function (res) {
        if (res.data['status_code'] === 200) {
          console.log(res.data['data'])
          that.topic_info = res.data['data']
        }
      }, function () {
        console.log('get topic_info faild')
      })
      return that.topic_info
    }
  },
  data () {
    return {
      topic_info: this.get_topic_info()
    }
  },
  props: {
    isShow: {
      type: Boolean,
      default: false,
      required: true
    },
    topicId: {
      default: 0,
      required: true
    }
  },
  watch: {
    topicId: function () {
      this.topic_info = this.get_topic_info()
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
  width: 50%;
  padding: 2px;
  margin:100px 50px;
  position: fixed;
  top: 0px;
  left: 20%;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 300;
  color: #17121c;
}
.foot_close{
  margin-bottom: 10px;
  margin-top: 10px;
}
#topic_info_table {
  padding:1px;
  font-size:  25px;
  margin-left: 11%;
  border:1px solid #F00;
  background:#F00
}
#topic_info_table td{
  background:#FFF
}
#head {
  font-size:  25px;
  width: 100px;
}
#info{
  width: 450px;
  font-size:  25px;
}

</style>
