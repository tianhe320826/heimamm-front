<template>
  <div class="container">
    <!-- 基础题库部分--Panic -->
    <!-- 卡片视图 -->
    <el-card>
      <el-row>
        <!-- 头部 -->
        <el-col class="title">
          <!-- 说明提示区域 -->
          <el-alert title="说明：目前支持学科和关键字条件筛选" type="info" :closable="false" class="PromptMessage" > </el-alert>
          <!-- 添加试题按钮 -->
          <el-button type="success" icon="el-icon-edit" class="AddQuestionButton" @click="$router.push('new')">新增试题</el-button>
        </el-col>
        <!-- 筛选栏目 -->
        <el-col>
          <!-- 筛选表单 -->
          <el-form label-width="75px" size="small">
            <el-row>
              <el-col :span="6">
                <el-form-item label="学科">
                  <el-select @change="changeSubject" v-model="requestParams.subjectID" style="width:100%">
                    <el-option v-for="item in subjectOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="二级目录">
                  <el-select v-model="requestParams.catalogID" style="width:100%">
                    <el-option v-for="item in directoryOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="标签">
                  <el-select v-model="requestParams.tags" style="width:100%">
                    <el-option v-for="item in tagOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="关键字">
                  <el-input v-model="requestParams.keyword"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="试题类型">
                  <el-select v-model="requestParams.questionType" style="width:100%">
                    <el-option v-for="item in questionTypeOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="难度">
                  <el-select v-model="requestParams.difficulty" style="width:100%">
                    <el-option v-for="item in diffOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="方向">
                  <el-select v-model="requestParams.direction" style="width:100%">
                    <el-option v-for="item in directionOptions" :key="item" :label="item" :value="item"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="录入人">
                  <el-select v-model="requestParams.creatorID" style="width:100%">
                    <el-option v-for="item in userOptions" :key="item.id" :label="item.username" :value="item.id"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="题目备注">
                  <el-input v-model="requestParams.remarks"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="企业简称">
                  <el-input v-model="requestParams.shortName"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="城市">
                  <!-- 城市 -->
                  <el-select @change="handleCity" v-model="requestParams.province" style="width:48%;margin-right:2%">
                    <el-option v-for="item in cityOptions" :key="item" :label="item" :value="item"></el-option>
                  </el-select>
                  <!-- 地区 -->
                  <el-select v-model="requestParams.city" style="width:50%">
                    <el-option v-for="item in areaOptions" :key="item" :label="item" :value="item"></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item style="text-align:right">
                  <el-button @click="clear()">清除</el-button>
                  <el-button @click="filter()" type="primary">搜索</el-button>
                </el-form-item>
              </el-col>
            </el-row>
          </el-form>
        </el-col>
        <el-col>
          <!-- 数据提示区域 -->
          <el-alert :title="`数据一共${total}条`" type="info" show-icon :closable="false"> </el-alert>
        </el-col>
      </el-row>
      <!-- 数据列表 -->
      <el-table :data="questions">
        <el-table-column label="试题编号" prop="number" width="120px"></el-table-column>
        <el-table-column label="学科" prop="subject"></el-table-column>
        <el-table-column label="目录" prop="catalog"></el-table-column>
        <el-table-column label="题型">
          <template slot-scope="scope">
            {{ questionType.find(item => item.value === +scope.row.questionType).label }}
          </template>
        </el-table-column>
        <el-table-column label="题干" width="280px">
          <template slot-scope="scope">
            <div v-html="scope.row.question"></div>
          </template>
        </el-table-column>
        <el-table-column label="录入时间" width="180">
          <template slot-scope="scope">
            {{ scope.row.addDate | parseTimeByString }}
          </template>
        </el-table-column>
        <el-table-column label="难度">
          <template slot-scope="scope">
            {{ difficulty.find(item => item.value === +scope.row.difficulty).label }}
          </template>
        </el-table-column>
        <el-table-column label="录入人" prop="creator"></el-table-column>
        <el-table-column label="操作" width="200px">
          <template slot-scope="scope">
            <el-button plain type="primary" size="small" circle icon="el-icon-view" title="预览" @click="openPreviewDialog(scope.row)"></el-button>
            <el-button plain type="success" size="small" circle icon="el-icon-edit" title="修改" @click="$router.push(`new?id=${scope.row.id}`)"></el-button>
            <el-button plain type="danger" size="small" circle icon="el-icon-delete" title="删除" @click="delQuestion(scope.row)"></el-button>
            <el-button plain type="warning" size="small" circle icon="el-icon-check" @click="addChoice(scope.row)" title="加入精选"></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-pagination
        style="margin-top:20px;text-align:right"
        background
        layout="prev, pager, next, sizes, jumper"
        :total="total"
        :page-size="requestParams.pagesize"
        :current-page="requestParams.page"
        @current-change="pager"
        :page-sizes="[5, 10, 20, 50]"
        @size-change="handleSizeChange"
      ></el-pagination>
    </el-card>
    <!-- 预览 -->
    <questions-preview ref="questionPreview" :data="questionInfo"></questions-preview>
  </div>
</template>

<script>
// 获取学科请求方法
import { simple as subjectList } from '@/api/hmmm/subjects'
// 获取目录请求方法
import { simple as directoryList } from '@/api/hmmm/directorys'
// 获取录入人请求方法
import { simple as userList } from '@/api/base/users'
// 获取 难度 试题类型 方向 请求方法
import { difficulty, questionType, direction } from '@/api/hmmm/constants'
// 获取标签请求方法
import { simple as tagList } from '@/api/hmmm/tags'
// 获取省份城市请求方法
import { provinces as getPro, citys as getCity } from '@/api/hmmm/citys'
// 获取试题数据 删除 添加精品 请求方法
import { list as questionList, remove as questionDel, choiceAdd } from '@/api/hmmm/questions'
// 导入预览组件
import QuestionsPreview from '../components/questions-preview'
export default {
  name: 'Questions',
  components: {
    QuestionsPreview
  },
  data () {
    return {
      questionInfo: {},
      questionType, // 试题类型
      difficulty, // 难度
      total: 0, // 数据长度
      // 请求到的数据对象
      requestParams: {
        subjectID: '', // 学科ID
        difficulty: '', // 难度
        questionType: '', // 试题类型
        tags: '', // 标签
        province: '', // 省份
        city: '', // 城市
        keyword: '', // 关键字
        remarks: '', // 评论
        shortName: '', // 企业简称
        direction: '', // 方向
        creatorID: '', // 录入人ID
        catalogID: '', // 目录ID
        page: 1,
        pagesize: 5
      },
      // 试题数据存放
      questions: [],
      // 学科选项
      subjectOptions: [],
      // 难度选项
      diffOptions: difficulty,
      // 试题类型选项
      questionTypeOptions: questionType,
      // 标签选项
      tagOptions: [],
      // 城市
      cityOptions: getPro(),
      // 地区 PS:选择了城市时才发生联动
      areaOptions: [],
      // 方向选项
      directionOptions: direction,
      // 录入人
      userOptions: [],
      // 目录
      directoryOptions: []
    }
  },
  async created () {
    // 学科下拉选项
    const { data: subjectArr } = await subjectList()
    // console.log(subjectArr)
    this.subjectOptions = subjectArr
    // 录入人下拉选项
    const { data: userArr } = await userList()
    // console.log(userArr)
    this.userOptions = userArr
    // 调用获取数据列表
    this.getList()
  },

  methods: {
    // 学科 二级目录 标签 联动下拉栏
    async changeSubject (subjectID) {
      // 清空二级目录
      this.requestParams.catalogID = ''
      // 清空标签
      this.requestParams.tags = ''
      if (subjectID) {
        const { data: directoryrArr } = await directoryList()
        // console.log(directoryrArr)
        this.directoryOptions = directoryrArr
        const { data: tagArr } = await tagList()
        // console.log(tagArr)
        this.tagOptions = tagArr
      } else {
        this.directoryOptions = []
        this.tagOptions = []
      }
    },
    // 城市联动选择下拉栏
    handleCity (cityName) {
      // 清空地区
      this.requestParams.city = ''
      this.areaOptions = getCity(cityName)
    },
    // 获取数据列表
    async getList () {
      const { data: questions } = await questionList()
      console.log(questions)
      this.questions = questions.items
      this.total = questions.counts
    },

    // 清除
    clear () {
      for (const key in this.requestParams) {
        if (key !== 'page' && key !== 'pagesize') this.requestParams[key] = null
      }
    },

    // 搜索
    filter () {
      this.requestParams.page = 1
      this.getList()
    },

    handleSizeChange (size) {
      this.requestParams.pagesize = size
      this.getList()
    },

    pager (cp) {
      this.requestParams.page = cp
      this.getList()
    },
    // 删除操作
    async delQuestion (question) {
      await this.$confirm('此操作将永久删除该题目, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      await questionDel(question)
      this.$message.success('删除成功')
      this.getList()
    },
    // 加入精选操作
    async addChoice (question) {
      await this.$confirm('此操作将该题目加入精选, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'info'
      })
      await choiceAdd({ id: question.id, choiceState: 1 })
      this.$message.success('加入精选成功')
      this.getList()
    },
    // 预览操作
    openPreviewDialog (questionInfo) {
      this.questionInfo = questionInfo
      this.$nextTick(() => {
        // this.$refs.questionInfo
      })
    }
  }
}
</script>

<style scoped lang="scss">
.container {
  padding: 0 10px;
  margin: 10px 0;
.title {
  display: flex;
  margin-bottom: 15px;
  .PromptMessage {
    color: #ffcad4;
    background-color: #fff;
  }
}

}
</style>
