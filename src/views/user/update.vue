<template>
  <div class="update-avatar">
    <el-upload
      class="avatar-uploader"
      action="your-api-url-for-uploading-avatar"
      :show-file-list="false"
      :on-success="handleAvatarSuccess"
      :before-upload="beforeAvatarUpload"
    >
      <img v-if="imageUrl" :src="imageUrl" class="avatar">
      <i v-else class="el-icon-plus avatar-uploader-icon"></i>
    </el-upload>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imageUrl: ''
    };
  },
  methods: {
    handleAvatarSuccess(response, file) {
      this.imageUrl = URL.createObjectURL(file.raw);
      // 你可以在这里添加代码来处理上传成功后的逻辑，例如更新用户的头像
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === 'image/jpeg';
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG 格式!');
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!');
      }
      return isJPG && isLt2M;
    }
  }
};
</script>

<style>
.update-avatar .avatar-uploader {
  cursor: pointer;
  width: 178px;
  height: 178px;
}
.update-avatar .avatar {
  width: 100%;
  height: 100%;
  display: block;
}
</style>
