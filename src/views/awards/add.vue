<template>
  <div>
    <h2>添加奖惩信息</h2>

    <el-form :model="newRewardOrPenalty" ref="newRewardOrPenaltyForm" label-width="100px" class="add-reward-form">
      <el-form-item label="学号">
        <el-input v-model="newRewardOrPenalty.id" size="small"></el-input>
      </el-form-item>
      <el-form-item label="姓名">
        <el-input v-model="newRewardOrPenalty.name" size="small"></el-input>
      </el-form-item>
      <el-form-item label="班级">
        <el-input v-model="newRewardOrPenalty.class" size="small"></el-input>
      </el-form-item>
      <el-form-item label="专业">
        <el-input v-model="newRewardOrPenalty.major" size="small"></el-input>
      </el-form-item>
      <el-form-item label="院系">
        <el-input v-model="newRewardOrPenalty.department" size="small"></el-input>
      </el-form-item>
      <el-form-item label="奖惩号">
        <el-input v-model="newRewardOrPenalty.rewardId" size="small"></el-input>
      </el-form-item>
      <el-form-item label="奖惩名">
        <el-input v-model="newRewardOrPenalty.rewardName" size="small"></el-input>
      </el-form-item>
      <el-form-item label="奖惩方案">
        <el-input v-model="newRewardOrPenalty.rewardPlan" size="small"></el-input>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" size="small" @click="addRewardOrPenalty">保存</el-button>
        <el-button size="small" @click="resetForm">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newRewardOrPenalty: {
        id: '',
        name: '',
        class: '',
        major: '',
        department: '',
        rewardId: '',
        rewardName: '',
        rewardPlan: ''
      }
    };
  },
  methods: {
    addRewardOrPenalty() {
      // 这里你可以将newRewardOrPenalty添加到数据中，例如发送到服务器或添加到本地存储。
      console.log('新的奖惩信息', this.newRewardOrPenalty);

      // 在成功添加数据后，可以重置表单。
      this.resetForm();
    },
    resetForm() {
      // 重置表单数据
      Object.keys(this.newRewardOrPenalty).forEach(key => {
        this.newRewardOrPenalty[key] = '';
      });
    }
  }
};
</script>

<style>
.add-reward-form {
  width: 50%;
  margin: 0 auto;
}

.el-form-item {
  margin-bottom: 16px;
}
</style>
