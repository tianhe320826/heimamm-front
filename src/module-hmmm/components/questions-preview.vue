<template>
  <div class="container">
      <el-row class="pvw" :gutter="20">
        <el-col :span="6">
          <span>[题型] : {{question.catalog}}</span>
        </el-col>
        <el-col :span="6">
          <span>[编号] : {{question.id}}</span>
        </el-col>
        <el-col :span="6">
          <span>[难度] : {{difficulty}}</span>
        </el-col>
        <el-col :span="6">
          <span>[标签] : {{question.tags}}</span>
        </el-col>
      </el-row>
      <el-row class="pvw" :gutter="20">
        <el-col :span="6">
          <span>[学科] : {{question.subject}}</span>
        </el-col>
        <el-col :span="6">
          <span>[目录] : {{question.catalogID}}</span>
        </el-col>
        <el-col :span="6">
          <span>[方向] : {{question.direction}}</span>
        </el-col>
      </el-row>
      <p class="median"></p>
      <!-- 题干 -->
      <el-row :gutter="20">
        <el-col :span="10">
          <span class="pvw">[题干] :
            <i v-html="myData.question"></i>
          </span>
          <p>选项:<span>( 以下选中的选项为正确答案 )</span></p>
          <div
            v-for="(item, index) in myData.options"
            :key="index"
          >
            <!-- 单选 -->
            <div v-if="question.questionType === '1'">
              <el-radio
                :value="item.isRight"
                label="1"
              >{{ item.code + '. ' + item.title }}</el-radio>
            </div>
            <!-- 多选 -->
            <div v-if="question.questionType === '2'">
              <el-checkbox
                :value="item.isRight ? true : false"
              >{{ item.code + '. ' + item.title }}</el-checkbox>
            </div>
          </div>
        </el-col>
      </el-row>
      <!-- 题干 -->
      <p class="median"></p>
      <!-- 参考答案 -->
      <el-row class="pvw" :gutter="20">
        <el-col :span="10">
          <span class="pvw">[参考答案] : </span>
          <el-button size="mini" type="danger" :src="question.videURL">视频答案预览</el-button>
        </el-col>
      </el-row>
      <!-- 参考答案 -->
      <p class="median"></p>
      <!-- 答案解析 -->
      <el-row :gutter="20">
        <el-col :span="10">
          <span class="pvw">[答案解析] :
            <i v-html="question.answer"></i>
          </span>
        </el-col>
      </el-row>
      <!-- 答案解析 -->
      <p class="median"></p>
      <!-- 题目备注 -->
      <el-row :gutter="20">
        <el-col :span="10">
          <span class="pvw">[题目备注] : {{question.remarks}}</span>
        </el-col>
      </el-row>
      <!-- 题目备注 -->
      <el-button
        @click="show"
        class="pvButton"
        size="mini"
        type="primary"
      >关闭</el-button>
  </div>
</template>

<script>
import { detail } from '@/api/hmmm/questions'

export default {
  created () {
    this.getList()
  },
  data() {
    return {
      radio: '',
      myData: {}
    }
  },
  props: {
    question: {
      type: Object,
      required: true
    }
  },
  computed: {
    difficulty: function () {
      if (this.myData.difficulty === '1') {
        return '简单'
      } else if (this.myData.difficulty === '2') {
        return '一般'
      } else {
        return '困难'
      }
    }
  },
  methods: {
    // 弹出框的隐藏，传值给父组件进行修改
    show () {
      this.$emit('updataButton')
    },
    
    // 请求基础题库详情
    async getList () {
      const res = await detail({ id: this.question.id })
      console.log(res)
      this.myData = res.data
    }
  }
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
