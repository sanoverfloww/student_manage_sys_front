<template>
  <div>
    <!-- 搜索栏 -->
    <div class="search-bar">
      <el-input
        placeholder="请输入学号或姓名"
        v-model="searchQuery"
        clearable
        @clear="filterStudents"
        @input="filterStudents"
        style="width: 300px;"
      >
        <el-button slot="append" icon="el-icon-search" @click="filterStudents"></el-button>
      </el-input>
    </div>
    <el-table :data="filteredStudents" border>
      <el-table-column label="学号" prop="student_id" header-align="center"></el-table-column>
      <el-table-column label="姓名" prop="name" header-align="center"></el-table-column>
      <el-table-column label="班级" prop="class" header-align="center"></el-table-column>
      <el-table-column label="专业" prop="major" header-align="center"></el-table-column>
      <el-table-column label="院系" prop="college" header-align="center"></el-table-column>
      <el-table-column label="操作" header-align="center">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="editStudent(scope.row)">
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
      <span>确定要删除该学生吗？</span>
      <div slot="footer" class="dialog-footer">
        <el-button @click="deleteStudentConfirm" type="danger">删除</el-button>
        <el-button @click="deleteDialogVisible = false">取消</el-button>
      </div>
    </el-dialog>
    <el-dialog
      title="编辑学生信息"
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
          <el-input v-model="editDialogForm.name"></el-input>
        </el-form-item>
        <el-form-item label="班级">
          <el-input v-model="editDialogForm.class"></el-input>
        </el-form-item>
        <el-form-item label="专业">
          <el-input v-model="editDialogForm.major"></el-input>
        </el-form-item>
        <el-form-item label="院系">
          <el-input v-model="editDialogForm.college"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="editStudentConfirm" type="primary">保存</el-button>
        <el-button @click="editDialogVisible = false">取消</el-button>
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
      filteredStudents: [],
      students: null,
      deleteDialogVisible: false,
      editDialogVisible: false,
      selectedStudent: null,
      editDialogForm: {
        student_id: '',
        name: '',
        class: '',
        major: '',
        college: ''
      }
    }
  },
  async mounted() {
    await this.fetchStudents()
  },
  methods: {
    async fetchStudents() {
      try {
        const response = await axios.get('/api/students')
        this.students = response.data
        this.filteredStudents = [...this.students]
      } catch (error) {
        console.error('Error fetching students:', error)
      }
    },
    filterStudents() {
      if (this.searchQuery.trim() === '') {
        this.filteredStudents = [...this.students]
      } else {
        const query = this.searchQuery.toLowerCase()
        this.filteredStudents = this.students.filter(
          student =>
            student.student_id.toLowerCase().includes(query) ||
            student.name.toLowerCase().includes(query)
        )
      }
    },
    editStudent(student) {
      this.selectedStudent = student
      this.editDialogForm = { ...student } // Populate the edit form with the student's data
      this.editDialogVisible = true
    },
    showDeleteConfirm(student) {
      this.selectedStudent = student
      this.deleteDialogVisible = true
    },
    async deleteStudentConfirm() {
      if (this.selectedStudent) {
        try {
          // 发送 DELETE 请求到后端以删除选定的学生
          const response = await axios.delete(`http://localhost:5000/students/${this.selectedStudent.student_id}`);

          // 检查响应是否表示成功
          if (response.data.message) {
            // 从本地数据中删除学生并更新视图
            const index = this.students.findIndex(student => student.student_id === this.selectedStudent.student_id);
            if (index > -1) {
              this.students.splice(index, 1)
              this.filteredStudents = [...this.students]
            }
            this.$message({
              message: '学生删除成功',
              type: 'success'
            })
          }
        } catch (error) {
          console.error('Error deleting student:', error)
          this.$message({
            message: '删除学生失败',
            type: 'error'
          })
        }
      }
      this.deleteDialogVisible = false
      this.selectedStudent = null
    },
    handleDeleteDialogClose(done) {
      if (this.deleteDialogVisible) {
        this.deleteDialogVisible = false
        this.selectedStudent = null
      }
      done()
    },
    async editStudentConfirm() {
      if (this.selectedStudent) {
        try {
          // 创建一个包含所需字段的新对象
          const dataToUpdate = {
            name: this.editDialogForm.name,
            class: this.editDialogForm.class,
            major: this.editDialogForm.major,
            college: this.editDialogForm.college
          }
          const response = await axios.put(
            'http://localhost:5000/students/' + this.selectedStudent.student_id,
            dataToUpdate, // 发送新的对象而不是整个 editDialogForm
            {
              headers: {
                'Content-Type': 'application/json'
              }
            }
          )
          if (response.data.message) {
            await this.fetchStudents()
            this.$message({
              message: '学生信息更新成功',
              type: 'success'
            })
          }
        } catch (error) {
          console.error('Error updating student:', error)
          this.$message({
            message: '更新学生信息失败',
            type: 'error'
          })
        }
      }
      this.editDialogVisible = false
      this.selectedStudent = null
    },
    handleEditDialogClose(done) {
      if (this.editDialogVisible) {
        this.editDialogVisible = false
        this.selectedStudent = null
        this.editDialogForm = {
          student_id: '',
          name: '',
          class: '',
          major: '',
          college: ''
        }
      }
      done()
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
