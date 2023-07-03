<template>
  <div class="export-grade-report">
    <h1 style="text-align: center">导出成绩单</h1>
    <el-form ref="form" :model="exportForm" label-width="80px">
      <el-form-item label="学号" prop="student_id">
        <el-input v-model="exportForm.student_id"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="exportGradeReport">导出成绩单</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      exportForm: {
        student_id: ''
      }
    }
  },
  methods: {
    exportGradeReport() {
      const studentId = this.exportForm.student_id
      axios.get(`http://localhost:5000/grades/export/${studentId}`, { responseType: 'blob' })
        .then(response => {
          const url = window.URL.createObjectURL(new Blob([response.data]))
          const link = document.createElement('a')
          link.href = url
          link.setAttribute('download', 'grade_report.pdf')
          document.body.appendChild(link)
          link.click()
          document.body.removeChild(link)
        })
        .catch(error => {
          console.error('Error exporting grade report:', error)
          this.$message({
            message: '导出成绩单时出现错误',
            type: 'error'
          })
        })
    }
  }
}
</script>

<style scoped>
.export-grade-report {
  margin: 30px;
}
</style>
