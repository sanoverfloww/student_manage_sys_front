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
      <el-table-column label="奖惩详情" header-align="center">
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
      <p>{{ rewardPlan }}</p>
      <div slot="footer" class="dialog-footer">
        <el-button @click="rewardPlanDialogVisible = false">关闭</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      searchQuery: '',
      filteredRewardsAndPenalties: [],
      rewardsAndPenalties: [],
      deleteDialogVisible: false,
      rewardPlanDialogVisible: false,
      selectedRewardAndPenalty: null,
      rewardPlan: ''
    };
  },
  async mounted() {
    await this.fetchRewardsAndPenalties();
  },
  methods: {
    async fetchRewardsAndPenalties() {
      try {
        const response = await axios.get('/api/rewards_and_penalties');
        this.rewardsAndPenalties = response.data;
        this.filteredRewardsAndPenalties = [...this.rewardsAndPenalties];
      } catch (error) {
        console.error('Error fetching rewards and penalties:', error);
      }
    },
    filterRewardsAndPenalties() {
      if (this.searchQuery.trim() === '') {
        this.filteredRewardsAndPenalties = [...this.rewardsAndPenalties];
      } else {
        const query = this.searchQuery.toLowerCase();
        this.filteredRewardsAndPenalties = this.rewardsAndPenalties.filter(
          item =>
            item.student_id.toLowerCase().includes(query) ||
            item.name.toLowerCase().includes(query)
        );
      }
    },
    showRewardPlan(item) {
      this.rewardPlan = item.reward_plan;
      this.rewardPlanDialogVisible = true;
    },
    showDeleteConfirm(item) {
      this.selectedRewardAndPenalty = item;
      this.deleteDialogVisible = true;
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
