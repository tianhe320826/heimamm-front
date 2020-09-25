<template>
  <div class="rdashboard-containe">
    <div class="app-container">
      <!-- 卡片面板 -->
      <el-card class="box-card">
        <!-- 新增按钮与说明 -->
        <div class="explain">
          <span class="font">说明：目前支持学科和关键字条件筛选</span>
          <el-button type="success" icon="el-icon-edit" size="small" @click="$router.push('new')">新增试题</el-button>
        </div>

        <!-- 表单区域 -->
        <el-form ref="formData" label-width="80px">
          <el-row>
            <el-col :span="6">
              <el-form-item label="学科">
                <el-select @change="changeSubject(formData.subjectID)" v-model="formData.subjectID" placeholder="请选择">
                  <el-option v-for="(item, index) in subjects" :key="index" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="二级目录">
                <el-select v-model="formData.catalogID" placeholder="请选择">
                  <el-option v-for="(item, index) in catalogs" :key="index" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="标签">
                <el-select v-model="formData.tagList" placeholder="请选择">
                  <el-option v-for="(item, index) in tags" :key="index" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
               <el-form-item label="关键字">
                 <el-input v-model="formData.keyword" placeholder="根据题干搜索"></el-input>
               </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="试题类型">
                <el-select v-model="formData.questionType" placeholder="请选择">
                  <el-option v-for="item in questions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="难度">
                <el-select v-model="formData.difficuly" placeholder="请选择">
                  <el-option v-for="item in difficulted" :key="item.value" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
               <el-form-item label="方向">
                 <el-select v-model="formData.direction" placeholder="请选择">
                   <el-option v-for="item in directions" :key="item" :label="item" :value="item"></el-option>
                 </el-select>
               </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="录入人">
                <el-select v-model="formData.creatorID" placeholder="请选择">
                  <el-option v-for="item in users" :key="item.id" :label="item.username" :value="item.id"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="题目备注">
                <el-input v-model="formData.remarks"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="企业简称">
                <el-input v-model="formData.shortName"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item class="city" label="城市">
                <!-- 城市 -->
                <el-select @change="getProvince" v-model="formData.province" placeholder="请选择">
                  <el-option
                    v-for="item in citys.provinces"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
                <!-- 区县 -->
                <el-select v-model="formData.city" placeholder="请选择">
                  <el-option
                    v-for="item in citys.cityData"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item>
                <el-button @keyup.enter="search" @click="search()">搜索</el-button>
                <el-button @click="clear()" type="primary">清除</el-button>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>

        <!-- 数据记录 -->
        <el-alert class="alert" type="info" show-icon :title="`数据一共${total}条`" >
        </el-alert>

        <!-- 数据表格 -->
        <el-table :data="questionList" style="width: 100%" >
            <el-table-column label="试题编号" prop="number" width="210px">
            </el-table-column>
            <el-table-column label="学科" prop="subject">
            </el-table-column>
            <el-table-column label="目录" prop="catalog">
            </el-table-column>
            <el-table-column label="题型">
              <template slot-scope="scope">
                {{ questionType.find(item => item.value === +scope.row.questionType).label }}
              </template>
            </el-table-column>
            <el-table-column label="题干">
              <template slot-scope="scope">
                <div v-html="scope.row.question"></div>
              </template>
            </el-table-column>
            <el-table-column label="录入时间">
              <template slot-scope="scope">
                {{ scope.row.addDate | parseTimeByString }}
              </template>
            </el-table-column>
            <el-table-column label="难度" prop="difficulty">
              <template slot-scope="scope">
                {{ difficulty.find(item => item.value === +scope.row.difficulty).label }}
              </template>
            </el-table-column>
            <el-table-column label="录入人" prop="creator">
            </el-table-column>
            <!-- 操作按钮 -->
            <el-table-column label="操作" width="230">
              <template slot-scope="scope">
                <el-row>
                <!-- 预览 -->
                <el-button @click="question(scope.row)" plain type="primary" icon="el-icon-view" circle></el-button>
                <!-- 编辑 -->
                <el-button @click="$router.push(`new?id=${scope.row.id}`)" plain type="success" icon="el-icon-edit" circle></el-button>
                <!-- 删除 -->
                <el-button @click="removeQuestion(scope.row)" plain type="danger" icon="el-icon-delete" circle></el-button>
                <!-- 加入精选 -->
                <el-button @click="addChoice(scope.row)" plain type="warning" icon="el-icon-check" circle></el-button>
              </el-row>
              </template>
            </el-table-column>
        </el-table>

         <!-- 分页 -->
        <div class="block">
          <el-pagination
            background
            layout="prev, pager, next, sizes, jumper"
            :total="total"
            :page-size="formData.pagesize"
            :page-sizes="[3, 5, 7, 10]"
            :current-page="formData.page"
            @current-change="handlePager"
            @size-change="handleSizeChange">
          </el-pagination>
        </div>
      </el-card>

      <!-- 预览对话框 -->
      <el-dialog title="题目预览" :visible.sync="dialogVisible">
        <questions-preview
          v-if="dialogVisible"
          :question="questionInfo"
          @updataButton="isDialogShow"
        ></questions-preview>
      </el-dialog>
    </div>
  </div>
</template>

<script>
// 学科列表
import { simple as subjectList } from '@/api/hmmm/subjects'
// 二级目录列表
import { simple as catalogList } from '@/api/hmmm/directorys'
// 难度 试题类型 方向
import { difficulty, questionType, direction } from '@/api/hmmm/constants'
// 标签列表
import { simple as tagsList } from '@/api/hmmm/tags'
// 录入人列表
import { simple as userList } from '@/api/base/users'
// 基础题库列表 删除题库 添加精品
import { list, remove, choiceAdd } from '@/api/hmmm/questions'
// 省市联动
import { provinces, citys } from '@/api/hmmm/citys'
// 导入预览框组件
import QuestionsPreview from '../components/questions-preview'

export default {
  components: {
    QuestionsPreview
  },
 
 data () {
    return {
      // 试题信息
      questionInfo: {},
      // 试题类型
      questionType,
      // 难度
      difficulty,
      // 控制预览对话框的显示与否
      dialogVisible: false,
      // 基础题库数据列表
      formData: {
        // 学科ID
        subjectID: null,
        // 关键字
        keyword: '',
        // 试题类型
        questionType: null,
        // 标签
        tagList: null,
        // 难度
        difficulty: null,
        // 方向
        direction: '',
        // 录入人ID
        creatorID: null,
        // 目录ID
        catalogID: null,
        // 题目备注
        remarks: '',
        // 企业简称
        shortName: '',
        // 城市
        province: null,
        // 区县
        city: null,
        // 每页大小
        pagesize: 10,
        // 页数
        page: 1
      },
      // 学科数据列表
      subjects: [],
      // 二级目录数据列表
      catalogs: [],
      // 标签数据列表
      tags: [],
      // 试题类型数据列表
      questions: questionType,
      // 难度数据列表
      difficulted: difficulty,
      // 方向数据列表
      directions: direction,
      // 录入人数据列表
      users: [],
      // 省市联动数据
      citys: {
        // 城市
        provinces: provinces(),
        // 只有选择了城市，区县才能选择
        cityData: []
      },
      // 试题数据列表
      questionList: [],
      // 试题数据总条数
      total: 0
    }
  },

  async created () {
    // 获取省市联动数据
    this.getProvince()
    // 获取试题数据列表
    this.getList()
    // 学科的数据列表
    const { data: subjectArr } = await subjectList()
    // console.log(subjectArr)
    this.subjects = subjectArr
    // 录入人的数据列表
    const { data: userArr } = await userList()
    // console.log(userArr)
    this.users = userArr
  },

  methods: {
    // 搜索
    search () {
      this.getList()
    },

    // 清除
    clear () {
      for(var key in this.formData){
        if (key != 'page' && key != 'pagesize') {
          this.formData[key] = null
        }
      }
    },
    
    // 删除试题操作
    async removeQuestion (question) {
      await this.$confirm('此操作永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      try {
        await remove(question)
        this.$message.success('删除完成')
        this.getList()
      } catch (error) {
        this.$message.error('删除失败')
      }
    },

    // 加入精选操作
    async addChoice (question) {
      await this.$confirm('此操作会将该题目加入精选, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'info'
      })
      try {
        await choiceAdd({ id: question.id, choiceState: 1 })
        this.$message.success('加入精选成功')
        this.getList()
      } catch (error) {
        this.$message.error('加入精选失败')
      }
    },

    // 获取到市,下辖的区县
    getProvince (cityName) {
      // this.formData.city = ''
      this.citys.cityData = citys(cityName)
    },

    // 获取列表数据
    async getList () {
      // const params = this.formData
      const { data: questions } = await list( this.formData )
      // console.log(questions)
      this.questionList = questions.items
      this.total = questions.counts
    },

    // 当前页数
    handlePager (page) {
      this.formData.page = page
      this.getList()
    },

    // 每页条数
    handleSizeChange (size) {
      this.formData.pagesize = size
      this.getList()
    },

    // 二级目录 和 标签
    async changeSubject (subjectID) {
      if (subjectID) {
        const { data: directoryrArr } = await catalogList(subjectID)
        this.catalogs = directoryrArr
        // console.log(this.catalogs)
        const { data: tagArr } = await tagsList(subjectID)
        this.tags = tagArr
        // console.log(this.tags)
      } else {
        this.catalogs = []
        this.tags = []
      }
    },
  
    // 预览功能
    question (e) {
      this.questionInfo = e
      this.dialogVisible = true
    },

    // 关闭对话框
    isDialogShow () {
      this.dialogVisible = false
    },
  
  }
}
</script>

<style scoped lang='scss'>
  .explain {
    display: flex;
    justify-content: space-between;
    margin-bottom: 15px;
    .font {
      font-size: 12px;
      color: pink
    }
  }
  .el-input {
    width: 217px;
  }
  .city .el-select {
    width: 105px;
    margin-right: 3px;
  }
  .alert {
    margin: 20px 0px;
    span {
      font-size: 15px;
    }
  }
  .block {
    display: flex;
    justify-content: flex-end;
    margin-top: 20px;
  }
</style>
