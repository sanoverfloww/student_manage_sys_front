<template>
  <div class="add-reward-and-penalty">
    <h1>添加新的奖惩信息</h1>
    <el-form ref="form" :model="newRewardAndPenaltyForm" label-width="80px">
      <el-form-item label="学号" prop="student_id" :rules="[{ required: true, message: '请输入学号', trigger: 'blur' }, { validator: validateStudentId, trigger: 'blur' }]">
        <el-input v-model="newRewardAndPenaltyForm.student_id"></el-input>
      </el-form-item>
      <el-form-item label="姓名">
        <el-input v-model="newRewardAndPenaltyForm.name"></el-input>
      </el-form-item>
      <el-form-item label="班级">
        <el-input v-model="newRewardAndPenaltyForm.class"></el-input>
      </el-form-item>
      <el-form-item label="专业">
        <el-input v-model="newRewardAndPenaltyForm.major"></el-input>
      </el-form-item>
      <el-form-item label="学院">
        <el-input v-model="newRewardAndPenaltyForm.college"></el-input>
      </el-form-item>
      <el-form-item label="奖惩编号">
        <el-input v-model="newRewardAndPenaltyForm.reward_id"></el-input>
      </el-form-item>
      <el-form-item label="奖惩名称">
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
        callback()
      }
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
    }
  }
}
</script>

<style scoped>
.add-reward-and-penalty {
  margin: 30px;
}
</style>
