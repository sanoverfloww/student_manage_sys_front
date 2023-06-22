<template>
  <div class="add-student">
    <h1>添加新学生</h1>
    <el-form ref="form" :model="newStudentForm" :rules="rules" label-width="80px">
      <el-form-item label="学号" prop="student_id">
        <el-input v-model="newStudentForm.student_id"></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop="name">
        <el-input v-model="newStudentForm.name"></el-input>
      </el-form-item>
      <el-form-item label="班级" prop="class">
        <el-input v-model="newStudentForm.class"></el-input>
      </el-form-item>
      <el-form-item label="专业" prop="major">
        <el-input v-model="newStudentForm.major"></el-input>
      </el-form-item>
      <el-form-item label="院系" prop="college">
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
      },
      rules: {
        student_id: [
          { required: true, message: '请输入学号', trigger: 'blur' },
          { pattern: /^PB\d{8}$/, message: '学号格式错误，应为PB+8位数字', trigger: 'blur' }
        ],
        name: [
          { required: true, message: '请输入姓名', trigger: 'blur' }
        ],
        class: [
          { required: true, message: '请输入班级', trigger: 'blur' }
        ],
        major: [
          { required: true, message: '请输入专业', trigger: 'blur' }
        ],
        college: [
          { required: true, message: '请输入院系', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    async addStudent() {
      this.$refs.form.validate((valid) => {
        if (!valid) return false;
        axios.post(
          'http://localhost:5000/students',
          this.newStudentForm,
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
        ).then(response => {
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
            this.$refs.form.resetFields();
          }
        }).catch(error => {
          console.error('Error adding student:', error)
          this.$message({
            message: '添加学生失败',
            type: 'error'
          })
        })
      });
    }
  }
}
</script>

<style>
.add-student {
  margin: 30px;
}
</style>
