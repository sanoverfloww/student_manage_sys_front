<template>
  <div>
    <!-- 搜索栏 -->
    <div class="search-bar">
      <el-input
        placeholder="请输入学号或姓名"
        v-model="searchQuery"
        clearable
        @clear="filterGrades"
        @input="filterGrades"
        style="width: 300px;"
      >
        <el-button slot="append" icon="el-icon-search" @click="filterGrades"></el-button>
      </el-input>
    </div>
    <el-table :data="filteredGrades" border>
      <el-table-column label="学号" prop="student_id" header-align="center"></el-table-column>
      <el-table-column label="姓名" prop="name" header-align="center"></el-table-column>
      <el-table-column label="课程" prop="course" header-align="center"></el-table-column>
      <el-table-column label="成绩" prop="grade" header-align="center"></el-table-column>
      <el-table-column label="操作" header-align="center">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="editGrade(scope.row)">
            编辑
          </el-button>
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
      <span>确定要删除这个成绩吗？</span>
      <div slot="footer" class="dialog-footer">
        <el-button @click="deleteGradeConfirm" type="danger">删除</el-button>
        <el-button @click="deleteDialogVisible = false">取消</el-button>
      </div>
    </el-dialog>
    <el-dialog
      title="编辑成绩"
      :visible="editDialogVisible"
      width="30%"
      @close="editDialogVisible = false"
      :before-close="handleEditDialogClose"
    >
      <el-form :model="editDialogForm" label-width="80px">
        <el-form-item label="学号">
          <el-input v-model="editDialogForm.student_id" disabled></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="editDialogForm.name" disabled></el-input>
        </el-form-item>
        <el-form-item label="课程">
          <el-input v-model="editDialogForm.course" disabled></el-input>
        </el-form-item>
        <el-form-item label="成绩">
          <el-input v-model="editDialogForm.grade"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editGradeConfirm" type="primary">保存</el-button>
        <el-button @click="editDialogVisible = false">取消</el-button>
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
      filteredGrades: [],
      grades: [],
      deleteDialogVisible: false,
      editDialogVisible: false,
      selectedGrade: null,
      editDialogForm: {
        student_id: '',
        name: '',
        course: '',
        grade: ''
      }
    };
  },
  async mounted() {
    await this.fetchGrades();
  },
  methods: {
    async fetchGrades() {
      try {
        const response = await axios.get('/api/grades')
        this.grades = response.data
        this.filteredGrades = [...this.grades]
      } catch (error) {
        console.error('Error fetching grades:', error)
      }
    },
    filterGrades() {
      if (this.searchQuery.trim() === '') {
        this.filteredGrades = [...this.grades]
      } else {
        const query = this.searchQuery.toLowerCase()
        this.filteredGrades = this.grades.filter(
          grade =>
            grade.student_id.toLowerCase().includes(query) ||
            grade.name.toLowerCase().includes(query) ||
            grade.course.toLowerCase().includes(query)
        );
      }
    },
    editGrade(grade) {
      this.selectedGrade = grade
      this.editDialogForm = { ...grade }
      this.editDialogVisible = true
    },
    showDeleteConfirm(grade) {
      this.selectedGrade = grade
      this.deleteDialogVisible = true
    },
    async deleteGradeConfirm() {
      if (this.selectedGrade) {
        try {
          const response = await axios.delete(
            `http://localhost:5000/grades/${this.selectedGrade.student_id}/${this.selectedGrade.course}`
          )
          if (response.data.message) {
            const index = this.grades.findIndex(
              grade =>
                grade.student_id === this.selectedGrade.student_id &&
                grade.course === this.selectedGrade.course
            )
            if (index > -1) {
              this.grades.splice(index, 1);
              this.filteredGrades = [...this.grades];
            }
            this.$message({
              message: '成绩删除成功',
              type: 'success'
            })
          }
        } catch (error) {
          console.error('Error deleting grade:', error);
          this.$message({
            message: '删除成绩失败',
            type: 'error'
          });
        }
      }
      this.deleteDialogVisible = false;
      this.selectedGrade = null;
    },
    handleDeleteDialogClose(done) {
      if (this.deleteDialogVisible) {
        this.deleteDialogVisible = false;
        this.selectedGrade = null;
      }
      done();
    },
    async editGradeConfirm() {
      if (this.selectedGrade) {
        try {
          const dataToUpdate = {
            name: this.editDialogForm.name,
            course: this.editDialogForm.course,
            grade: this.editDialogForm.grade
          }
          const response = await axios.put(
            `http://localhost:5000/grades/${this.selectedGrade.student_id}/${this.selectedGrade.course}`,
            dataToUpdate,
            {
              headers: {
                'Content-Type': 'application/json'
              }
            }
          )

          if (response.data.message) {
            await this.fetchGrades()
            this.$message({
              message: '成绩更新成功',
              type: 'success'
            })
          }
        } catch (error) {
          console.error('Error updating grade:', error)
          this.$message({
            message: '更新成绩失败',
            type: 'error'
          })
        }
      }
      this.editDialogVisible = false
      this.selectedGrade = null
    },
    handleEditDialogClose(done) {
      if (this.editDialogVisible) {
        this.editDialogVisible = false
        this.selectedGrade = null
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
