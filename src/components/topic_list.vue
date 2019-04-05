<template>
  <div id="topic_list">
    <!-- 搜索界面-->
    <div class="bar">
      <input type="text" v-model="searchStringTeacher" placeholder="教师姓名" />
      <input type="text" v-model="searchStringStudent" placeholder="学生姓名" />
      <input type="text" v-model="searchStringTopic" placeholder="课题名称" />
    </div>
    <!-- 课题显示 -->
    <table id="topics">
      <tr>
        <th v-for="(value, key) in columnList" :key='key'>{{ value }}</th>
      </tr>
      <tr v-for='(item, key) in inforList' :key='key'>
        <td v-for='(it, key) in item.slice(0,item.length - 1)' :key='key' style="width: 11%">{{ it }}</td>
        <td>
          <div id="topic_info">
            <button v-on:click="Show(item[item.length - 1])">详情</button>
            <button v-on:click="selectTopic(item[item.length - 1])">选择</button>
          </div>
        </td>
      </tr>
    </table>
    <!-- 分页 -->
    <pagination :total="total_topic" @pagechange="pagechange"></pagination>
    <!-- 详情页  -->
    <topicInfo @hidden="hiddenShow" :is-show="topicInfoShow" :topic-id=selectTopicId></topicInfo>
  </div>
</template>

<script>
import guide from './guide'
import pagination from './page'
import topicInfo from './topic_info'
import axios from 'axios'
export default {
  name: 'topic_list',
  components: {
    guide,
    pagination,
    topicInfo
  },
  data: function () {
    this.getTopic(1)
    return {
      columnList: ['编号', '课题名称', '指导教师', '选课学生', '是否完成', '创建时间', '选课时间', '完成时间', '操作'],
      topicInfoShow: false,
      searchStringTeacher: '',
      searchStringStudent: '',
      searchStringTopic: '',
      inforList: [],
      nowPage: 1,
      total_topic: 10,
      selectTopicId: 1
    }
  },
  props: {
    loginUser: {
      default: ''
    },
    userType: {
      default: -1
    }

  },
  watch: {
    searchStringTeacher: function () {
      this.getTopic(this.nowPage)
    },
    searchStringStudent: function () {
      this.getTopic(this.nowPage)
    },
    searchStringTopic: function () {
      this.getTopic(this.nowPage)
    }
  },
  methods: {
    pagechange: function (currentPage) {
      this.nowPage = currentPage
      console.log(currentPage)
      this.getTopic(currentPage)
      // ajax请求, 向后台发送 currentPage, 来获取对应的数据
    },
    getTopic: function (currentPage) {
      let that = this
      axios.get('/api/topic_list', { params: {
        page: currentPage,
        searchStringTeacher: this.searchStringTeacher,
        searchStringStudent: this.searchStringStudent,
        searchStringTopic: this.searchStringTopic
      }}).then(function (res) {
        if (res.data['status_code'] === 200) {
          that.total_topic = res.data['data']['total']
          that.inforList = res.data['data']['topics']
        }
      }, function () {
        console.log('get topic faild')
      })
    },
    selectTopic: function (topicId) {
      if (this.loginUser === '') {
        alert('请先登陆')
      } else {
        if (confirm('确认选择次课题？')) {
          let that = this
          axios.get('/api/select_topic', { params: {
            topic_id: topicId,
            login_user: that.loginUser
          }}).then(function (res) {
            if (res.data['status_code'] === 200) {
              alert(res.data['data'])
              that.getTopic(that.nowPage)
            }
          }, function () {
            alert('faild')
            console.log('get topic faild')
          })
        }
      }
    },
    Show: function (item) {
      this.selectTopicId = item
      this.topicInfoShow = true
    },
    hiddenShow: function () {
      this.topicInfoShow = false
    }
  }
}
</script>

<style>
#topic_list{
  border:0px solid rgba(182, 165, 147, 0.84);
}
#topics{
  border-collapse:collapse;
  border-spacing:0;
  margin:0;
  padding:0;
  color: #17121c;
  text-align:center;
  font-size:  25px;
  width: 100%;
}
#topic_info{
  font-size:  20px;
  margin: auto;
}
.bar{
  width: 100%;
  padding: 8px;
  float: right;
  overflow: hidden;
  font-size: 15px;
}

.bar input{
  background:#fff no-repeat 13px 13px;
  background-image:url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyBpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuMC1jMDYwIDYxLjEzNDc3NywgMjAxMC8wMi8xMi0xNzozMjowMCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bWxuczp4bXBNTT0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL21tLyIgeG1sbnM6c3RSZWY9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZVJlZiMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNSBXaW5kb3dzIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOkU5NEY0RTlFMTA4NzExRTM5RTEzQkFBQzMyRjkyQzVBIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOkU5NEY0RTlGMTA4NzExRTM5RTEzQkFBQzMyRjkyQzVBIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6RTk0RjRFOUMxMDg3MTFFMzlFMTNCQUFDMzJGOTJDNUEiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6RTk0RjRFOUQxMDg3MTFFMzlFMTNCQUFDMzJGOTJDNUEiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4DjA/RAAABK0lEQVR42pTSQUdEURjG8dOY0TqmPkGmRcqYD9CmzZAWJRHVRIa0iFYtM6uofYaiEW2SRJtEi9YxIklp07ZkWswu0v/wnByve7vm5ee8M+85zz1jbt9Os+WiGkYdYxjCOx5wgFeXUHmtBSzpcCGa+5BJTCjEP+0nKWAT8xqe4ArPGEEVC1hHEbs2oBwdXkM7mj/JLZrad437sCGHOfUtcziutuYu2v8XUFF/4f6vMK/YgAH1HxkBYV60AR31gxkBYd6xAeF3VzMCwvzOBpypX8V4yuFRzX2d2gD/l5yjH4fYQEnzkj4fae5rJulF2sMXVrAsaTWttRFu4Osb+1jEDT71/ZveyhouTch2fINQL9hKefKjuYFfuznXWzXMTabyrvfyIV3M4vhXgAEAUMs7K0J9UJAAAAAASUVORK5CYII=);

  border: none;
  width: 30%;
  line-height: 19px;
  padding: 11px 0;

  border-radius: 2px;
  box-shadow: 0 2px 8px #c4c4c4 inset;
  text-align: left;
  font-size: 14px;
  font-family: inherit;
  color: #738289;
  font-weight: bold;
  outline: none;
  text-indent: 40px;
}
#topics tr:nth-child(odd){
  background: rgba(149, 149, 149, 0.65);
}
#topics tr:nth-child(even){
  background: rgba(73, 73, 73, 0.23);
}
</style>
