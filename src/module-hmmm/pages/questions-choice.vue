<template>
  <div class="rdashboard-containe">
    <div class="app-container">
      <!-- 卡片面板 -->
      <el-card class="box-card">
        <!-- 新增按钮与说明 -->
        <div class="explain">
          <span class="font">说明：目前支持学科和关键字条件筛选</span>
          <el-button type="success" icon="el-icon-edit" size="small" @click="$router.push(`new/${formData.subjectID}`)">新增试题</el-button>
        </div>
        <!-- 表单区域 -->
        <el-form ref="formData" label-width="80px">
          <el-row>
            <el-col :span="6">
              <el-form-item label="学科">
                <el-select @change="changeSubject(formData.subjectID)" v-model="formData.subjectID" placeholder="请选择" clearable>
                  <el-option v-for="(item, index) in subjects" :key="index" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="二级目录">
                <el-select v-model="formData.catalogID" placeholder="请选择" clearable>
                  <el-option v-for="(item, index) in catalogs" :key="index" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="标签">
                <el-select v-model="formData.tagList" placeholder="请选择" clearable>
                  <el-option v-for="(item, index) in tags" :key="index" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="关键字">
                <el-input v-model="formData.keyword" placeholder="根据题干搜索" clearable></el-input>
              </el-form-item>
            </el-col>

            <el-col :span="6">
              <el-form-item label="试题类型">
                <el-select v-model="formData.questionType" placeholder="请选择" clearable>
                  <el-option v-for="item in questions" :key="item.value" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="难度">
                <el-select v-model="formData.difficuly" placeholder="请选择" clearable>
                  <el-option v-for="item in difficulted" :key="item.value" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="方向">
                <el-select v-model="formData.direction" placeholder="请选择" clearable>
                  <el-option v-for="item in directions" :key="item" :label="item" :value="item"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="录入人">
                <el-select v-model="formData.creatorID" placeholder="请选择" clearable>
                  <el-option v-for="item in users" :key="item.id" :label="item.username" :value="item.id"></el-option>
                </el-select>
              </el-form-item>
            </el-col>

            <el-col :span="6">
              <el-form-item label="题目备注">
                <el-input v-model="formData.remarks" clearable></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="企业简称">
                <el-input v-model="formData.shortName" clearable></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item class="city" label="城市">
                <!-- 城市 -->
                <el-select @change="getProvince" v-model="formData.province" placeholder="请选择" clearable>
                  <el-option v-for="item in citys.provinces" :key="item" :label="item" :value="item"></el-option>
                </el-select>
                <!-- 区县 -->
                <el-select v-model="formData.city" placeholder="请选择" clearable>
                  <el-option v-for="item in citys.cityData" :key="item" :label="item" :value="item"></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item>
                <el-button @click="clear">清除</el-button>
                <el-button type="primary" @keyup.enter="search" @click="search">搜索</el-button>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>
        <!-- Tab栏切换 -->
        <el-tabs v-model="activeName" type="card" @tab-click="handleTabsClick">
          <el-tab-pane label="全部" name="all"></el-tab-pane>
          <el-tab-pane label="待审核" name="0"></el-tab-pane>
          <el-tab-pane label="已审核" name="1"></el-tab-pane>
          <el-tab-pane label="已拒绝" name="2"></el-tab-pane>
        </el-tabs>
        <!-- 数据记录 -->
        <el-alert class="alert" type="info" show-icon :title="`数据一共${total}条`"> </el-alert>
        <!-- 数据表格 -->
        <el-table :data="questionList">
          <el-table-column label="试题编号" prop="number"></el-table-column>
          <el-table-column label="学科" prop="subject"></el-table-column>
          <el-table-column label="目录" prop="catalog"></el-table-column>
          <el-table-column label="题型" prop="questionType">
            <template slot-scope="scope">
              <span v-if="scope.row.questionType === '1'">单选</span>
              <span v-if="scope.row.questionType === '2'">多选</span>
              <span v-if="scope.row.questionType === '3'">简答</span>
            </template>
          </el-table-column>
          <el-table-column label="题干" min-width="200">
            <template slot-scope="scope">
              <div v-html="scope.row.question"></div>
            </template>
          </el-table-column>
          <el-table-column label="录入时间" min-width="200">
            <template slot-scope="scope">
              {{ scope.row.addDate | parseTimeByString }}
            </template>
          </el-table-column>
          <el-table-column label="难度" prop="difficulty">
            <template slot-scope="scope">
              <span v-if="scope.row.chkState === 1">简单</span>
              <span v-if="scope.row.chkState === 2">一般</span>
              <span v-if="scope.row.chkState === 3">困难</span>
            </template>
          </el-table-column>
          <el-table-column label="录入人" prop="creator"></el-table-column>
          <!-- 审核状态 -->
          <el-table-column label="审核状态" prop="chkState">
            <template slot-scope="scope">
              <span v-if="scope.row.chkState === 0">待审核</span>
              <span v-if="scope.row.chkState === 1">已审核</span>
              <span v-if="scope.row.chkState === 2">已拒绝</span>
            </template>
          </el-table-column>
          <!-- 审核意见 -->
          <el-table-column label="审核意见" prop="chkRemarks"></el-table-column>
          <!-- 审核人 -->
          <el-table-column label="审核人" prop="chkUser"></el-table-column>
          <!-- 发布状态 -->
          <el-table-column label="发布状态" prop="publishState">
            <template slot-scope="scope">
              <span v-if="scope.row.chkState === 1">发布</span>
              <span v-if="scope.row.chkState === 2">已发布</span>
              <span v-if="scope.row.chkState === 3">已下架</span>
            </template>
          </el-table-column>
          <!-- 操作按钮 -->
          <el-table-column label="操作" fixed="right" width="230px" class="operation-btn">
            <template slot-scope="scope">
              <el-row type="flex" justify="space-around">
                <!-- 预览 -->
                <el-link @click="question(scope.row)" :underline="false" type="primary">预览</el-link>
                <!-- 审核 -->
                <el-link @click="checkDialogVisible = true" :underline="false" type="primary">审核</el-link>
                <!-- 修改 -->
                <el-link @click="$router.push(`new?id=${scope.row.id}`)" type="primary" :underline="false">修改</el-link>
                <!-- 上架 -->
                <el-link v-if="scope.row.publishState === 1" :underline="false" type="primary" @click="choicePublishState(scope.row)">上架</el-link>
                <el-link v-else-if="scope.row.publishState === 0" type="primary" @click="choicePublishState(scope.row)">下架</el-link>
                <!-- 删除 -->
                <el-link @click="removeQuestion(scope.row)" :underline="false" type="primary">删除</el-link>
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
            @size-change="handleSizeChange"
          >
          </el-pagination>
        </div>
      </el-card>
      <!-- 预览对话框 -->
      <el-dialog :visible.sync="previewDialogVisible">
        <questions-preview v-if="previewDialogVisible" :question="questionInfo" @updataButton="previewDialogVisible"></questions-preview>
      </el-dialog>
      <!-- 审核对话框 -->
      <el-dialog title="试题审核" width="400px" :visible.sync="checkDialogVisible">
        <el-form>
          <el-radio-group v-model="checkForm.choiceState">
            <el-radio :label="true">通过</el-radio>
            <el-radio :label="false">拒绝</el-radio>
          </el-radio-group>
          <br />
          <el-input type="textarea" placeholder="请输入审核意见" required style="width: 300px; margin: 20px 0 20px 0" v-model="checkForm.textarea"> </el-input>
          <el-button>取消</el-button>
          <el-button type="primary">确认</el-button>
        </el-form>
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
// 基础题库 删除题库 上架下架
import { choice, remove, choicePublish } from '@/api/hmmm/questions'
// 省市联动
import { provinces, citys } from '@/api/hmmm/citys'
// 导入预览框组件
import QuestionsPreview from '../components/questions-preview'

export default {
  components: {
    QuestionsPreview
  },
  data() {
    return {
      // 试题信息
      questionInfo: {},
      // 试题类型
      questionType,
      // 难度
      difficulty,
      // 控制预览对话框的显示与否
      previewDialogVisible: false,
      // 控制审核对话框显示
      checkDialogVisible: false,
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
      // 试题类型数据列表 不能引入进来直接使用
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
      total: 0,
      // tab栏默认选择
      activeName: 'all',
      // 审核表单数据
      checkForm: {
        id: 0,
        choiceState: false,
        textarea: ''
      }
    }
  },
  created() {
    // 请求省市联动数据
    this.Init()
    // 请求试题列表数据
    this.getList()
  },
  methods: {
    // 省市联动
    async Init() {
      // 获取省市联动数据
      this.getProvince()
      // 获取试题数据列表
      this.getList()
      // 学科的数据列表
      const { data: subjectArr } = await subjectList()
      this.subjects = subjectArr
      // 录入人的数据列表
      const { data: userArr } = await userList()
      this.users = userArr
    },
    // 删除试题操作
    async removeQuestion(question) {
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
    // 获取到市,下辖的区县
    getProvince(cityName) {
      this.citys.cityData = citys(cityName)
    },
    // 获取列表数据
    async getList() {
      const { data: res } = await choice({
        subjectID: this.formData.subjectID,
        keyword: this.formData.keyword,
        chkState: this.activeName === 'all' ? null : this.activeName - 0
      })
      if (this.activeName === 'all') {
        this.questionList = res.items
      } else if (this.activeName === '0') {
        this.questionList = res.items
      } else if (this.activeName === '1') {
        this.questionList = res.items
      } else {
        this.questionList = res.items
      }
      this.total = res.counts
    },
    // 当前页数
    handlePager(page) {
      this.formData.page = page
      this.getList()
    },
    // 每页条数
    handleSizeChange(size) {
      this.formData.pagesize = size
      this.getList()
    },
    // 二级目录 和 标签
    async changeSubject(subjectID) {
      if (subjectID) {
        const { data: directoryrArr } = await catalogList(subjectID)
        this.catalogs = directoryrArr
        const { data: tagArr } = await tagsList(subjectID)
        this.tags = tagArr
      } else {
        this.catalogs = []
        this.tags = []
      }
    },
    // 搜索
    search() {
      this.getList()
    },
    // 清除
    clear() {
      for (var key in this.formData) {
        if (key !== 'page' && key !== 'pagesize') {
          this.formData[key] = null
        }
      }
    },
    // tab栏点击切换
    handleTabsClick(tab) {
      this.getList()
    },
    // 上架下架
    async choicePublishState(row) {
      // 请求接口的参数有问题
      const params = {}
      params.id = row.id
      params.PublishState = row.PublishState
      await this.$confirm('您确定下架这道题目码?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      try {
        await choicePublish(params)
        this.$message.success('上架成功')
        this.getList()
      } catch (error) {
        this.$message.error('上架失败')
      }
    }
  }
}
</script>

<style scoped lang="scss">
.explain {
  display: flex;
  justify-content: space-between;
  margin-bottom: 15px;
  .font {
    font-size: 12px;
    color: pink;
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
.el-link {
  padding: 5px;
}
</style>
