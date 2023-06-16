<template>
  <div>
    <el-table :data="students" border>
      <el-table-column label="学号" prop="id" header-align="center"></el-table-column>
      <el-table-column label="姓名" prop="name" header-align="center"></el-table-column>
      <el-table-column label="班级" prop="class" header-align="center"></el-table-column>
      <el-table-column label="专业" prop="major" header-align="center"></el-table-column>
      <el-table-column label="院系" prop="department" header-align="center"></el-table-column>
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
          <el-input v-model="editDialogForm.id"></el-input>
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
          <el-input v-model="editDialogForm.department"></el-input>
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
export default {
  data() {
    return {
      students: [
        {
          id: '001',
          name: '张三',
          class: '1班',
          major: '计算机科学',
          department: '计算机学院'
        },
        {
          id: '002',
          name: '李四',
          class: '2班',
          major: '信息管理',
          department: '信息学院'
        },
        // 添加更多的学生数据...
      ],
      deleteDialogVisible: false,
      editDialogVisible: false,
      selectedStudent: null,
      editDialogForm: {
        id: '',
        name: '',
        class: '',
        major: '',
        department: ''
      }
    };
  },
  methods: {
    editStudent(student) {
      this.selectedStudent = student;
      this.editDialogForm = { ...student }; // Populate the edit form with the student's data
      this.editDialogVisible = true;
    },
    showDeleteConfirm(student) {
      this.selectedStudent = student;
      this.deleteDialogVisible = true;
    },
    deleteStudentConfirm() {
      if (this.selectedStudent) {
        const index = this.students.indexOf(this.selectedStudent);
        if (index > -1) {
          this.students.splice(index, 1);
          console.log('删除学生', this.selectedStudent);
        }
      }
      this.deleteDialogVisible = false;
      this.selectedStudent = null;
    },
    handleDeleteDialogClose(done) {
      if (this.deleteDialogVisible) {
        this.deleteDialogVisible = false;
        this.selectedStudent = null;
      }
      done();
    },
    editStudentConfirm() {
      if (this.selectedStudent) {
        const index = this.students.indexOf(this.selectedStudent);
        if (index > -1) {
          this.students.splice(index, 1, { ...this.editDialogForm }); // Update the student with the edited values
          console.log('编辑学生信息', this.editDialogForm);
        }
      }
      this.editDialogVisible = false;
      this.selectedStudent = null;
    },
    handleEditDialogClose(done) {
      if (this.editDialogVisible) {
        this.editDialogVisible = false;
        this.selectedStudent = null;
        this.editDialogForm = {
          id: '',
          name: '',
          class: '',
          major: '',
          department: ''
        };
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
