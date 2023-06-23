<template>
  <div class="add-grade">
    <h1 style="text-align: center">添加新成绩</h1>
    <el-form ref="form" :model="newGradeForm" :rules="rules" label-width="80px">
      <el-form-item label="学号" prop="student_id" >
        <el-input v-model="newGradeForm.student_id" ></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop="name" >
        <el-input v-model="newGradeForm.name" disabled></el-input>
      </el-form-item>
      <el-form-item label="课程" prop="course" >
        <el-input v-model="newGradeForm.course" ></el-input>
      </el-form-item>
      <el-form-item label="成绩" prop="grade" >
        <el-input v-model="newGradeForm.grade" ></el-input>
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
      },
      rules: {
        student_id: [
          { required: true, message: '请输入学号', trigger: 'blur' },
          // { pattern: /^PB\d{8}$/, message: '学号格式错误，应为PB+8位数字', trigger: 'blur' },
          { validator: this.validateStudentId, trigger: 'blur' },
          { validator: this.checkStudentIdExists, trigger: 'blur' }
        ],
        course: [
          { required: true, message: '请输入课程', trigger: 'blur' }
        ],
        grade: [
          { required: true, message: '请输入成绩', trigger: 'blur' },
          { validator: this.validateGrade, trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    validateStudentId(rule, value, callback) {
      const pattern = /^PB\d{8}$/
      if (!pattern.test(value)) {
        return callback(new Error('学号格式错误，应为PB+8位数字'))
      } else {
        // console.log('next is fetch')
        this.fetchStudentInfo(value)
        callback()
      }
    },
    validateGrade(rule, value, callback) {
      if (!Number.isInteger(Number(value)) || Number(value) < 0 || Number(value) > 100) {
        callback(new Error('成绩应为0-100的整数'))
      } else {
        callback()
      }
    },
    fetchStudentInfo(student_id) {
      axios.post('http://localhost:5000/student_info', { student_id })
        .then(response => {
          console.log('response data is', response.data)
          if (response.data) {
            this.newGradeForm.name = response.data.name
          }
        })
        .catch(error => {
          console.error('Error fetching student info:', error)
        })
    },
    async addGrade() {
      this.$refs.form.validate(async(valid) => {
        if (!valid) return false
        const gradeForm = { ...this.newGradeForm, grade: parseInt(this.newGradeForm.grade) }
        await axios.post(
          'http://localhost:5000/grades',
          gradeForm,
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
        ).then(response => {
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
        }).catch(error => {
          console.error('Error adding grade:', error)
          this.$message({
            message: '添加成绩时出现错误',
            type: 'error'
          })
        })
      })
    },
    async checkStudentIdExists(rule, value, callback) {
      if (!value) {
        return callback()
      }
      try {
        const response = await axios.post('http://localhost:5000/check_student_id', { student_id: value });
        if (response.data.exists) {
          callback()
        } else {
          callback(new Error('学号不存在'))
        }
      } catch (error) {
        console.error('Error checking student id:', error)
        callback(new Error('学号验证失败'))
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
