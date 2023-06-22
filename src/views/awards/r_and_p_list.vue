<template>
  <div>
    <!-- 搜索栏 -->
    <div class="search-bar">
      <el-input
        placeholder="请输入学号或姓名"
        v-model="searchQuery"
        clearable
        @clear="filterRewardsAndPenalties"
        @input="filterRewardsAndPenalties"
        style="width: 300px;"
      >
        <el-button slot="append" icon="el-icon-search" @click="filterRewardsAndPenalties"></el-button>
      </el-input>
    </div>
    <el-table :data="filteredRewardsAndPenalties" border>
      <el-table-column label="学号" prop="student_id" header-align="center"></el-table-column>
      <el-table-column label="姓名" prop="name" header-align="center"></el-table-column>
      <el-table-column label="班级" prop="class" header-align="center"></el-table-column>
      <el-table-column label="专业" prop="major" header-align="center"></el-table-column>
      <el-table-column label="学院" prop="college" header-align="center"></el-table-column>
      <el-table-column label="奖惩编号" prop="reward_id" header-align="center"></el-table-column>
      <el-table-column label="奖惩名称" prop="reward_name" header-align="center"></el-table-column>
      <el-table-column label="奖惩详情" prop="reward_plan" header-align="center">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="showRewardPlan(scope.row)">
            点击查看
          </el-button>
        </template>
      </el-table-column>
      <el-table-column label="操作" header-align="center">
        <template slot-scope="scope">
          <el-button type="danger" size="mini" @click="showDeleteConfirm(scope.row)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      title="确认删除"
      :visible="deleteDialogVisible"
      width="30%"
      @close="deleteDialogVisible = false"
      :before-close="handleDeleteDialogClose"
    >
      <span>确定要删除这个奖惩记录吗？</span>
      <div slot="footer" class="dialog-footer">
        <el-button @click="deleteRewardAndPenaltyConfirm" type="danger">删除</el-button>
        <el-button @click="deleteDialogVisible = false">取消</el-button>
      </div>
    </el-dialog>
    <el-dialog
      title="奖惩详情"
      :visible="rewardPlanDialogVisible"
      width="50%"
      @close="rewardPlanDialogVisible = false"
    >
      <div v-if="!editMode">
        <p>{{ rewardPlan }}</p>
      </div>
      <div v-else>
        <el-input
          type="textarea"
          :rows="4"
          placeholder="请输入奖惩详情"
          v-model="rewardPlan"
        ></el-input>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="toggleEditMode" v-if="!editMode">编辑</el-button>
        <el-button type="primary" @click="editRewardAndPenaltyConfirm" v-if="editMode">保存</el-button>
        <el-button @click="rewardPlanDialogVisible = false">关闭</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      searchQuery: '',
      filteredRewardsAndPenalties: [],
      rewardsAndPenalties: [],
      deleteDialogVisible: false,
      rewardPlanDialogVisible: false,
      selectedRewardAndPenalty: null,
      rewardPlan: '',
      viewDialogVisible: false,
      editMode: false
    }
  },
  async mounted() {
    await this.fetchRewardsAndPenalties()
  },
  methods: {
    async fetchRewardsAndPenalties() {
      try {
        const response = await axios.get('/api/rewards_and_penalties')
        this.rewardsAndPenalties = response.data
        this.filteredRewardsAndPenalties = [...this.rewardsAndPenalties]
      } catch (error) {
        console.error('Error fetching rewards and penalties:', error)
      }
    },
    filterRewardsAndPenalties() {
      if (this.searchQuery.trim() === '') {
        this.filteredRewardsAndPenalties = [...this.rewardsAndPenalties]
      } else {
        const query = this.searchQuery.toLowerCase()
        this.filteredRewardsAndPenalties = this.rewardsAndPenalties.filter(
          item =>
            item.student_id.toLowerCase().includes(query) ||
            item.name.toLowerCase().includes(query)
        )
      }
    },
    showRewardPlan(item) {
      console.log('plan is', item)
      this.selectedRewardAndPenalty = item
      this.rewardPlan = item.reward_plan
      this.rewardPlanDialogVisible = true
    },
    showDeleteConfirm(item) {
      this.selectedRewardAndPenalty = item
      this.deleteDialogVisible = true
    },
    async deleteRewardAndPenaltyConfirm() {
      if (this.selectedRewardAndPenalty) {
        try {
          const response = await axios.delete(
            `http://localhost:5000/rewards_and_penalties/${this.selectedRewardAndPenalty.reward_id}`
          )

          if (response.data.message) {
            const index = this.rewardsAndPenalties.findIndex(
              item => item.reward_id === this.selectedRewardAndPenalty.reward_id
            )
            if (index > -1) {
              this.rewardsAndPenalties.splice(index, 1)
              this.filteredRewardsAndPenalties = [...this.rewardsAndPenalties]
            }
            this.$message({
              message: '奖惩记录删除成功',
              type: 'success'
            })
          }
        } catch (error) {
          console.error('Error deleting reward or penalty:', error);
          this.$message({
            message: '删除奖惩记录失败',
            type: 'error'
          });
        }
      }
      this.deleteDialogVisible = false
      this.selectedRewardAndPenalty = null
    },
    handleDeleteDialogClose(done) {
      if (this.deleteDialogVisible) {
        this.deleteDialogVisible = false
        this.selectedRewardAndPenalty = null
      }
      done()
    },
    toggleEditMode() {
      this.editMode = !this.editMode
    },
    async editRewardAndPenaltyConfirm() {
      if (!this.editMode) {
        return
      }
      this.selectedRewardAndPenalty.reward_plan = this.rewardPlan
      console.log('selectedRewardAndPenalty:', this.selectedRewardAndPenalty);

      // 发送一个PUT请求到服务器以更新奖惩信息
      try {
        const response = await axios.put(
          `http://localhost:5000/rewards_and_penalties/${this.selectedRewardAndPenalty.student_id}/${this.selectedRewardAndPenalty.reward_id}`,
          this.selectedRewardAndPenalty,
          {
            headers: {
              'Content-Type': 'application/json'
            }
          }
        )

        if (response.data.message) {
          this.$message({
            message: '奖惩信息更新成功',
            type: 'success'
          })
          this.viewDialogVisible = false
          this.editMode = false
          await this.fetchRewardsAndPenalties() // 假设你有一个类似的方法来获取奖惩信息列表
        }
      } catch (error) {
        console.error('Error updating reward and penalty:', error)
        this.$message({
          message: '更新奖惩信息失败',
          type: 'error'
        })
      }
    }
  }
}
</script>

<style>
.search-bar {
  margin: 20px 0;
}
.dialog-footer {
  display: flex;
  justify-content: flex-end;
}
</style>
