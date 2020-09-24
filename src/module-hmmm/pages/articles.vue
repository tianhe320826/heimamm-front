<template>
  <div class="dashboard-container">
    <div class="app-container">
      <div class="container">
        <el-card shadow="never">
          <!-- 顶部栏 -->
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="关键字">
              <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="状态">
              <el-input v-model="form.name"></el-input>
            </el-form-item>
          </el-form>
          <!-- 顶部栏 -->

          <!-- 提示 -->
          <el-alert :title="`总共XXX条数据!`" type="info" :closable="false" show-icon></el-alert>
          <!-- 提示 -->

          <!-- table表格 -->

          <!-- table表格 -->

          <!-- 分页栏 -->
          <!-- 分页栏 -->
        </el-card>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      form: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      }
    }
  }
}
</script>

<style scoped lang='less'>
</style>
