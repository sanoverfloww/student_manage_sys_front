<template>
  <div class="add-reward-and-penalty">
    <h1 style="text-align: center">添加新的奖惩信息</h1>
    <el-form ref="form" :model="newRewardAndPenaltyForm" label-width="80px">
      <el-form-item label="学号" prop="student_id" :rules="[{ required: true, message: '请输入学号', trigger: 'blur' }, { validator: validateStudentId, trigger: 'blur' }, { validator: this.checkStudentIdExists, trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.student_id"></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop="name" :rules="[{ required: true, message: '请输入姓名', trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.name" disabled></el-input>
      </el-form-item>
      <el-form-item label="班级" prop="class" :rules="[{ required: true, message: '请输入班级', trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.class" disabled></el-input>
      </el-form-item>
      <el-form-item label="专业" prop="major" :rules="[{ required: true, message: '请输入专业', trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.major" disabled></el-input>
      </el-form-item>
      <el-form-item label="学院" prop="college" :rules="[{ required: true, message: '请输入学院', trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.college" disabled></el-input>
      </el-form-item>
      <el-form-item label="奖惩编号" prop="reward_id" :rules="[{ required: true, message: '请输入奖惩编号', trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.reward_id"></el-input>
      </el-form-item>
      <el-form-item label="奖惩名称" prop="reward_name" :rules="[{ required: true, message: '请输入奖惩名称', trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.reward_name"></el-input>
      </el-form-item>
      <el-form-item label="奖惩详情">
        <el-input type="textarea" v-model="newRewardAndPenaltyForm.reward_plan"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="addRewardAndPenalty">添加奖惩信息</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      newRewardAndPenaltyForm: {
        student_id: '',
        name: '',
        class: '',
        major: '',
        college: '',
        reward_id: '',
        reward_name: '',
        reward_plan: ''
      }
    }
  },
  methods: {
    validateStudentId(rule, value, callback) {
      const pattern = /^PB\d{8}$/
      if (!pattern.test(value)) {
        return callback(new Error('学号格式错误，应为PB+8位数字'));
      } else {
        this.fetchStudentInfo(value)
        callback()
      }
    },
    fetchStudentInfo(student_id) {
      axios.post('http://localhost:5000/student_info', { student_id })
        .then(response => {
          console.log('response data is', response.data)
          if (response.data) {
            this.newRewardAndPenaltyForm.name = response.data.name
            this.newRewardAndPenaltyForm.class = response.data.class
            this.newRewardAndPenaltyForm.major = response.data.major
            this.newRewardAndPenaltyForm.college = response.data.college
          }
        })
        .catch(error => {
          console.error('Error fetching student info:', error)
        })
    },
    async addRewardAndPenalty() {
      this.$refs.form.validate(async(valid) => {
        if (valid) {
          try {
            const response = await axios.post(
              'http://localhost:5000/rewards_and_penalties',
              this.newRewardAndPenaltyForm,
              {
                headers: {
                  'Content-Type': 'application/json'
                }
              }
            )
            if (response.data.message) {
              this.$message({
                message: '奖惩信息添加成功',
                type: 'success'
              })
              this.newRewardAndPenaltyForm = {
                student_id: '',
                name: '',
                class: '',
                major: '',
                college: '',
                reward_id: '',
                reward_name: '',
                reward_plan: ''
              }
            }
          } catch (error) {
            console.error('Error adding reward and penalty:', error)
            this.$message({
              message: '添加奖惩信息时出现错误',
              type: 'error'
            })
          }
        } else {
          return false
        }
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
.add-reward-and-penalty {
  margin: 30px;
}
</style>
