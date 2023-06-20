<template>
  <div>
    <div class="search-bar">
      <el-input
        placeholder="请输入学号、姓名或科目"
        v-model="searchQuery"
        clearable
        @clear="filterScores"
        @input="filterScores"
        style="width: 300px;"
      >
        <el-button slot="append" icon="el-icon-search" @click="filterScores"></el-button>
      </el-input>
    </div>

    <el-table :data="filteredScores" border>
      <el-table-column label="学号" prop="id" header-align="center"></el-table-column>
      <el-table-column label="姓名" prop="name" header-align="center"></el-table-column>
      <el-table-column label="课程" prop="course" header-align="center"></el-table-column>
      <el-table-column label="成绩" prop="score" header-align="center"></el-table-column>
      <el-table-column label="操作" header-align="center">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="editScore(scope.$index)">
            编辑
          </el-button>
          <el-button type="danger" size="mini" @click="showDeleteConfirm(scope.$index)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog
      title="编辑成绩"
      :visible="editDialogVisible"
      width="30%"
      @close="editDialogVisible = false"
      :before-close="handleEditDialogClose"
    >
      <el-form :model="editForm" ref="editForm" label-width="80px">
        <el-form-item label="学号">
          <el-input v-model="editForm.id"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="editForm.name"></el-input>
        </el-form-item>
        <el-form-item label="课程">
          <el-input v-model="editForm.course"></el-input>
        </el-form-item>
        <el-form-item label="成绩">
          <el-input v-model="editForm.score"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editScoreConfirm" type="primary">保存</el-button>
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
      <span>确定要删除该成绩吗？</span>
      <div slot="footer" class="dialog-footer">
        <el-button @click="deleteScoreConfirm" type="danger">删除</el-button>
        <el-button @click="deleteDialogVisible = false">取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: '',
      filteredScores: [],
      scores: [
        {
          id: '001',
          name: '张三',
          course: '数学',
          score: 90
        },
        {
          id: '002',
          name: '李四',
          course: '英语',
          score: 85
        },
        {
          id: '002',
          name: '李四',
          course: '语文',
          score: 70
        },
        {
          id: '002',
          name: '李四',
          course: '物理',
          score: 80
        },
        {
          id: '003',
          name: '李四',
          course: '数学',
          score: 83
        },
        // 添加更多的成绩数据...
      ],
      editDialogVisible: false,
      deleteDialogVisible: false,
      editForm: {
        id: '',
        name: '',
        course: '',
        score: ''
      },
      selectedScoreIndex: -1
    };
  },
  created() {
    // 初始化 filteredScores 为所有成绩
    this.filteredScores = [...this.scores];
  },
  methods: {
    filterScores() {
      if (this.searchQuery.trim() === '') {
        this.filteredScores = [...this.scores];
      } else {
        const query = this.searchQuery.toLowerCase();
        this.filteredScores = this.scores.filter(
          score =>
            score.id.toLowerCase().includes(query) ||
            score.name.toLowerCase().includes(query) ||
            score.course.toLowerCase().includes(query)
        );
      }
    },
    editScore(index) {
      this.selectedScoreIndex = index;
      const selectedScore = this.scores[index];
      this.editForm = {
        id: selectedScore.id,
        name: selectedScore.name,
        course: selectedScore.course,
        score: selectedScore.score
      };
      this.editDialogVisible = true;
    },
    editScoreConfirm() {
      if (this.selectedScoreIndex !== -1) {
        this.scores.splice(this.selectedScoreIndex, 1, {
          id: this.editForm.id,
          name: this.editForm.name,
          course: this.editForm.course,
          score: this.editForm.score
        });
        console.log('保存成绩', this.editForm);
      }
      this.editDialogVisible = false;
      this.selectedScoreIndex = -1;
      this.$refs.editForm.resetFields(); // Reset form validation state
    },
    showDeleteConfirm(index) {
      this.selectedScoreIndex = index;
      this.deleteDialogVisible = true;
    },
    deleteScoreConfirm() {
      if (this.selectedScoreIndex !== -1) {
        const deletedScore = this.scores.splice(this.selectedScoreIndex, 1);
        console.log('删除成绩', deletedScore);
      }
      this.deleteDialogVisible = false;
      this.selectedScoreIndex = -1;
    },
    handleEditDialogClose(done) {
      if (this.editDialogVisible) {
        this.editDialogVisible = false;
        this.selectedScoreIndex = -1;
        this.$refs.editForm.resetFields(); // Reset form validation state
      }
      done();
    },
    handleDeleteDialogClose(done) {
      if (this.deleteDialogVisible) {
        this.deleteDialogVisible = false;
        this.selectedScoreIndex = -1;
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
</style>
