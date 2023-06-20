<template>
  <div class="dashboard-container">
    <div class="dashboard-text">name: {{ name }}</div>
    <ul v-if="students">
      <li v-for="student in students" :key="student.id">
        {{ student.id }}
      </li>
    </ul>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import axios from 'axios'

export default {
  name: 'Dashboard',
  computed: {
    ...mapGetters([
      'name'
    ])
  },
  data() {
    return {
      students: null
    }
  },
  async mounted() {
    try {
      const response = await axios.get('/api/students') // 注意这里的 URL
      console.log('Response data:', response.data); // 打印响应数据
      this.students = response.data;
    } catch (error) {
      console.error('Error fetching students:', error);
    }
  }
}
</script>

<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}
</style>
