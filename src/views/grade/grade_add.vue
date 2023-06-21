<template>
  <div class="add-grade">
    <h1>添加新成绩</h1>
    <el-form :model="newGradeForm" label-width="80px">
      <el-form-item label="学号">
        <el-input v-model="newGradeForm.student_id"></el-input>
      </el-form-item>
      <el-form-item label="姓名">
        <el-input v-model="newGradeForm.name"></el-input>
      </el-form-item>
      <el-form-item label="课程">
        <el-input v-model="newGradeForm.course"></el-input>
      </el-form-item>
      <el-form-item label="成绩">
        <el-input v-model="newGradeForm.grade"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="addGrade">添加成绩</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      newGradeForm: {
        student_id: '',
        name: '',
        course: '',
        grade: ''
      }
    }
  },
  methods: {
    async addGrade() {
      try {
        const response = await axios.post(
          'http://localhost:5000/grades',
          this.newGradeForm,
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
        )
        if (response.data.message) {
          this.$message({
            message: '成绩添加成功',
            type: 'success'
          });
          this.newGradeForm = {
            student_id: '',
            name: '',
            course: '',
            grade: ''
          }
        }
      } catch (error) {
        console.error('Error adding grade:', error)
        this.$message({
          message: '添加成绩时出现错误',
          type: 'error'
        })
      }
    }
  }
}
</script>

<style scoped>
.add-grade {
  margin: 30px;
}
</style>
