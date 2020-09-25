<template>
  <div class="container">
    <el-row class="pvw" :gutter="20">
      <el-col :span="6">
        <span>[题型] :</span>
      </el-col>
      <el-col :span="6">
        <span>[编号] :</span>
      </el-col>
      <el-col :span="6">
        <span>[难度] :</span>
      </el-col>
      <el-col :span="6">
        <span>[标签] :</span>
      </el-col>
    </el-row>
    <el-row class="pvw" :gutter="20">
      <el-col :span="6">
        <span>[学科] :</span>
      </el-col>
      <el-col :span="6">
        <span>[目录] :</span>
      </el-col>
      <el-col :span="6">
        <span>[方向] :</span>
      </el-col>
    </el-row>
    <p class="median"></p>
    <!-- 题干 -->
    <el-row :gutter="20">
      <el-col :span="10">
        <span class="pvw">[题干] :</span>
        <p>选项:<span>( 以下选中的选项为正确答案 )</span></p>
      </el-col>
    </el-row>
    <!-- 题干 -->
    <p class="median"></p>
    <!-- 参考答案 -->
    <el-row class="pvw" :gutter="20">
      <el-col :span="10">
        <span class="pvw">[题干] :</span>
        <el-button size="mini" type="danger">视频答案预览</el-button>
      </el-col>
    </el-row>
    <!-- 参考答案 -->
    <p class="median"></p>
    <!-- 答案解析 -->
    <el-row :gutter="20">
      <el-col :span="10">
        <span class="pvw">[答案解析] :</span>
      </el-col>
    </el-row>
    <!-- 答案解析 -->
    <p class="median"></p>
    <!-- 题目备注 -->
    <el-row :gutter="20">
      <el-col :span="10">
        <span class="pvw">[题目备注] :</span>
      </el-col>
    </el-row>
    <!-- 题目备注 -->
    <!-- <el-button type="primary" @click="dialogVisible = false">确 定</el-button> -->
    <!-- <el-button class="pvButton" size="mini" type="primary">关闭</el-button> -->
  </div>
</template>

<script>
export default {
  name: 'kkkk',
  data() {
    return {
      dialogVisible: this.dialogShow
    }
  },
  props: {
    dialogShow: {
      type: Boolean,
      requiest: true
    }
  },
  mounted() {},
  methods: {}
}
</script>

<style scoped lang='less'>
.container {
  margin-bottom: 40px;
  /deep/.el-row.pvw,
  .pvw {
    color: #333;
    font-weight: 700;
    margin-bottom: 25px;
    .el-button {
      margin-left: 15px;
    }
  }
  .median {
    border: 0.5px solid #eee;
  }
  .pvButton {
    float: right;
    margin-top: 20px;
  }
}
</style>
