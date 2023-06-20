<template>
  <div>
    <div class="search-bar">
      <el-input
        placeholder="请输入学号或姓名"
        v-model="searchText"
        clearable
        @clear="handleSearch"
        @input="handleSearch"
        style="width: 300px;"
      >
        <el-button slot="append" icon="el-icon-search" @click="filterStudents"></el-button>
      </el-input>
    </div>
    <el-table :data="filteredRewardsAndPenalties" border>
      <el-table-column label="学号" prop="id" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="姓名" prop="name" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="班级" prop="class" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="专业" prop="major" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="院系" prop="department" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="奖惩号" prop="rewardId" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="奖惩名" prop="rewardName" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="奖惩方案" prop="rewardPlan" header-align="center" :align="centerAlign"></el-table-column>
      <el-table-column label="操作" header-align="center">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="editRewardOrPenalty(scope.$index)">
            编辑
          </el-button>
          <el-button type="danger" size="mini" @click="showDeleteConfirm(scope.$index)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog
      title="编辑奖惩信息"
      :visible="editDialogVisible"
      width="50%"
      @close="editDialogVisible = false"
      :before-close="handleEditDialogClose"
    >
      <el-form :model="editForm" ref="editForm" label-width="100px">
        <el-form-item label="学号">
          <el-input v-model="editForm.id"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="editForm.name"></el-input>
        </el-form-item>
        <el-form-item label="班级">
          <el-input v-model="editForm.class"></el-input>
        </el-form-item>
        <el-form-item label="专业">
          <el-input v-model="editForm.major"></el-input>
        </el-form-item>
        <el-form-item label="院系">
          <el-input v-model="editForm.department"></el-input>
        </el-form-item>
        <el-form-item label="奖惩号">
          <el-input v-model="editForm.rewardId"></el-input>
        </el-form-item>
        <el-form-item label="奖惩名">
          <el-input v-model="editForm.rewardName"></el-input>
        </el-form-item>
        <el-form-item label="奖惩方案">
          <el-input v-model="editForm.rewardPlan"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editRewardOrPenaltyConfirm" type="primary">保存</el-button>
        <el-button @click="editDialogVisible = false">取消</el-button>
      </div>
    </el-dialog>

    <el-dialog
      title="确认删除"
      :visible="deleteDialogVisible"
      width="30%"
      @close="deleteDialogVisible = false"
      :before-close="handleDeleteDialogClose"
    >
      <span>确定要删除该奖惩信息吗？</span>
      <div slot="footer" class="dialog-footer">
        <el-button @click="deleteRewardOrPenaltyConfirm" type="danger">删除</el-button>
        <el-button @click="deleteDialogVisible = false">取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchText: '',
      filteredRewardsAndPenalties: [],
      rewardsAndPenalties: [
        {
          id: '001',
          name: '张三',
          class: '1班',
          major: '计算机科学',
          department: '计算机学院',
          rewardId: '001',
          rewardName: '优秀奖学金',
          rewardPlan: '3000元奖学金'
        },
        {
          id: '002',
          name: '李四',
          class: '2班',
          major: '电子工程',
          department: '电子信息学院',
          rewardId: '002',
          rewardName: '违纪处分',
          rewardPlan: '停课一周'
        },
        // 添加更多的奖惩信息数据...
      ],
      editDialogVisible: false,
      deleteDialogVisible: false,
      editForm: {
        id: '',
        name: '',
        class: '',
        major: '',
        department: '',
        rewardId: '',
        rewardName: '',
        rewardPlan: ''
      },
      selectedRewardOrPenaltyIndex: -1,
      centerAlign: 'center'
    };
  },
  created() {
    // 初始化 filteredStudents 为所有学生
    this.filteredRewardsAndPenalties = [...this.rewardsAndPenalties];
  },
  methods: {
    handleSearch() {
      if (this.searchText === "") {
        this.filteredRewardsAndPenalties = this.rewardsAndPenalties;
      } else {
        this.filteredRewardsAndPenalties = this.rewardsAndPenalties.filter(item =>
          item.id.includes(this.searchText) || item.name.includes(this.searchText)
        );
      }
    },
    editRewardOrPenalty(index) {
      this.selectedRewardOrPenaltyIndex = index;
      const selectedRewardOrPenalty = this.rewardsAndPenalties[index];
      this.editForm = {
        id: selectedRewardOrPenalty.id,
        name: selectedRewardOrPenalty.name,
        class: selectedRewardOrPenalty.class,
        major: selectedRewardOrPenalty.major,
        department: selectedRewardOrPenalty.department,
        rewardId: selectedRewardOrPenalty.rewardId,
        rewardName: selectedRewardOrPenalty.rewardName,
        rewardPlan: selectedRewardOrPenalty.rewardPlan
      };
      this.editDialogVisible = true;
    },
    editRewardOrPenaltyConfirm() {
      if (this.selectedRewardOrPenaltyIndex !== -1) {
        this.rewardsAndPenalties.splice(this.selectedRewardOrPenaltyIndex, 1, {
          id: this.editForm.id,
          name: this.editForm.name,
          class: this.editForm.class,
          major: this.editForm.major,
          department: this.editForm.department,
          rewardId: this.editForm.rewardId,
          rewardName: this.editForm.rewardName,
          rewardPlan: this.editForm.rewardPlan
        });
        console.log('保存奖惩信息', this.editForm);
      }
      this.editDialogVisible = false;
      this.selectedRewardOrPenaltyIndex = -1;
      this.$refs.editForm.resetFields(); // Reset form validation state
    },
    showDeleteConfirm(index) {
      this.selectedRewardOrPenaltyIndex = index;
      this.deleteDialogVisible = true;
    },
    deleteRewardOrPenaltyConfirm() {
      if (this.selectedRewardOrPenaltyIndex !== -1) {
        const deletedRewardOrPenalty = this.rewardsAndPenalties.splice(this.selectedRewardOrPenaltyIndex, 1);
        console.log('删除奖惩信息', deletedRewardOrPenalty);
      }
      this.deleteDialogVisible = false;
      this.selectedRewardOrPenaltyIndex = -1;
    },
    handleEditDialogClose(done) {
      if (this.editDialogVisible) {
        this.editDialogVisible = false;
        this.selectedRewardOrPenaltyIndex = -1;
        this.$refs.editForm.resetFields(); // Reset form validation state
      }
      done();
    },
    handleDeleteDialogClose(done) {
      if (this.deleteDialogVisible) {
        this.deleteDialogVisible = false;
        this.selectedRewardOrPenaltyIndex = -1;
      }
      done();
    }
  }
};
</script>

<style>
.el-table th .cell {
  text-align: center;
}

.el-table td .cell {
  text-align: center;
}

.search-bar {
  text-align: center;
  margin-bottom: 20px;
  margin-top: 10px;
}
</style>
