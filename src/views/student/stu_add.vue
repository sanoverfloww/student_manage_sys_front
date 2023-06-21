<template>
  <div class="add-student">
    <h1>添加新学生</h1>
    <el-form :model="newStudentForm" label-width="80px">
      <el-form-item label="学号">
        <el-input v-model="newStudentForm.student_id"></el-input>
      </el-form-item>
      <el-form-item label="姓名">
        <el-input v-model="newStudentForm.name"></el-input>
      </el-form-item>
      <el-form-item label="班级">
        <el-input v-model="newStudentForm.class"></el-input>
      </el-form-item>
      <el-form-item label="专业">
        <el-input v-model="newStudentForm.major"></el-input>
      </el-form-item>
      <el-form-item label="院系">
        <el-input v-model="newStudentForm.college"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="addStudent">添加学生</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      newStudentForm: {
        student_id: '',
        name: '',
        class: '',
        major: '',
        college: ''
      }
    }
  },
  methods: {
    async addStudent() {
      try {
        const response = await axios.post(
          'http://localhost:5000/students',
          this.newStudentForm,
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
        )
        if (response.data.message) {
          this.$message({
            message: '学生添加成功',
            type: 'success'
          });
          this.newStudentForm = {
            student_id: '',
            name: '',
            class: '',
            major: '',
            college: ''
          }
        }
      } catch (error) {
        console.error('Error adding student:', error)
        this.$message({
          message: '添加学生失败',
          type: 'error'
        })
      }
    }
  }
}
</script>

<style>
.add-student {
  margin: 30px;
}
</style>
