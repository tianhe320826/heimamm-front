<template>
  <div class="container">
    <el-row :gutter="20">
      <el-col :span="6">
        <span class="pvw">【 题型 】 :</span> <i>{{ questionType }}</i>
      </el-col>
      <el-col :span="6">
        <span class="pvw">【 编号 】 :</span> <i>{{ detailsList.id }}</i>
      </el-col>
      <el-col :span="6">
        <span class="pvw">【 难度 】 :</span> <i>{{ difficulty }}</i>
      </el-col>
      <el-col :span="6">
        <span class="pvw">【 标签 】 :</span> <i>{{ detailsList.tags }}</i>
      </el-col>
    </el-row>
    <el-row :gutter="20">
      <el-col :span="6">
        <span class="pvw">【 学科 】 :</span> <i>{{ detailsList.subjectName }}</i>
      </el-col>
      <el-col :span="6">
        <span class="pvw">【 目录 】 :</span> <i>{{ detailsList.catalogID }}</i>
      </el-col>
      <el-col :span="6">
        <span class="pvw">【 方向 】 :</span> <i>{{ detailsList.direction }}</i>
      </el-col>
    </el-row>
    <p class="median"></p>
    <!-- 题干 -->
    <el-row :gutter="20">
      <el-col :span="10">
        <span class="pvw">【 题干 】 :</span>
        <div v-html="detailsList.question">{{ detailsList.question }}</div>
        <p><span>选项:</span>( 以下选中的选项为正确答案 )</p>
      </el-col>
    </el-row>
    <!-- 题干 -->
    <p class="median"></p>
    <!-- 参考答案 -->
    <el-row class="pvw" :gutter="20">
      <el-col :span="10">
        <span class="pvw">【 参考答案 】 :</span>
        <el-button size="mini" type="danger">视频答案预览</el-button>
      </el-col>
    </el-row>
    <!-- 参考答案 -->
    <p class="median"></p>
    <!-- 答案解析 -->
    <el-row :gutter="20">
      <el-col :span="10">
        <span class="pvw">【 答案解析 】 :</span>
        <div v-html="detailsList.answer"></div>
      </el-col>
    </el-row>
    <!-- 答案解析 -->
    <p class="median"></p>
    <!-- 题目备注 -->
    <el-row :gutter="20">
      <el-col :span="10">
        <span class="pvw">【 题目备注 】:</span> <i>{{ detailsList.remarks }}</i>
      </el-col>
    </el-row>
    <!-- 题目备注 -->
  </div>
</template>

<script>
import { detail } from '@/api/hmmm/questions.js'
export default {
  name: 'kkkk',
  data() {
    return {
      detailsList: {}
      // dialogVisible: this.dialogShow
    }
  },
  computed: {
    difficulty() {
      if (this.detailsList.difficulty === 0) {
        return '简单'
      } else if (this.detailsList.difficulty === 1) {
        return '一般'
      } else {
        return '复杂'
      }
    },
    questionType() {
      if (this.detailsList.difficulty === 1) {
        return '简单'
      } else if (this.detailsList.difficulty === 2) {
        return '单选'
      } else {
        return '多选'
      }
    }
  },
  props: {
    dialogID: {
      type: [Number, String],
      requiest: true
    }
  },
  mounted() {
    this.getDetailData()
  },
  methods: {
    async getDetailData() {
      try {
        const { data } = await detail({
          id: this.dialogID
        })
        this.detailsList = data
        console.log(data)
      } catch (error) {
        console.log(error)
      }
    }
  }
}
</script>

<style scoped lang='less'>
.container {
  margin-bottom: 40px;
  /deep/.el-row.pvw,
  .pvw {
    color: #666;
    font-weight: 700;
    margin-right: 10px;
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
