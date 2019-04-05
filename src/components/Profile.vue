<template>
  <div class="profile">
    <div v-if="UserType === 1">
      <student-profile :login-user="loginUser"></student-profile>
    </div>
    <div v-else-if="UserType === 2">
      <teacher-profile :login-user="loginUser"></teacher-profile>
      <!-- 老师 -->
    </div>
  </div>
</template>

<script>
import guide from './guide'
import studentProfile from './student_profile'
import teacherProfile from './teacher_profile'
export default {
  name: 'Profile',
  components: {
    guide,
    studentProfile,
    teacherProfile
  },
  props: {
    loginUser: {
      default: ''
    },
    UserType: {
      default: -1
    }
  },
  data () {
    return {
    }
  },
  method: {
  }
}
</script>
