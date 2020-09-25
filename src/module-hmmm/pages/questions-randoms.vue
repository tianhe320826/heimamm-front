<template>
  <div class="dashboard-container">
    <div class="app-container">
      <div class="container">
        <el-card shadow="never">
          <!-- 顶部栏 -->
          <div>
            <div class="please-list-input-suffix">
              <el-row :gutter="24">
                <el-col :span="20">
                  关键字：
                  <el-input class="Keyword" clearable placeholder="请输入内容" prefix-icon="el-icon-search" v-model="queryInfo.keyword" size="small" @keyup.enter.native="getQuestionData"></el-input>
                </el-col>
                <el-col :span="4">
                  <div class="grid-content bg-purple">
                    <el-button size="mini" type="info" @click="queryInfo.keyword = ''">清除</el-button>
                    <el-button size="mini" type="primary" @click="getQuestionData">搜索</el-button>
                  </div>
                </el-col>
              </el-row>
            </div>
            <!-- <el-row :gutter="24">
              <el-col :span="20">
                关键字：
                <el-input class="Keyword" clearable placeholder="请输入内容" prefix-icon="el-icon-search" v-model="queryInfo.keyword" size="small" @keyup.enter.native="getQuestionData"></el-input>
              </el-col>
              <el-col :span="4">
                <div class="grid-content bg-purple">
                  <el-button size="mini" type="info" @click="queryInfo.keyword = ''">清除</el-button>
                  <el-button size="mini" type="primary" @click="getQuestionData">搜索</el-button>
                </div>
              </el-col>
            </el-row> -->
          </div>
          <!-- 顶部栏 -->
          <!-- 提示 -->
          <el-alert :title="`总共${counts}条数据!`" type="info" :closable="false" show-icon></el-alert>
          <!-- 提示 -->
          <!-- table表格 -->
          <div>
            <el-table class="randomsTable" :data="isTableData" style="width: 100%">
              <el-table-column width="200px" prop="id" label="编号"></el-table-column>
              <el-table-column width="80px" prop="questionType" label="题型">
                <template slot-scope="scope">
                  <el-tag v-if="scope.row.questionType === '1'">单选</el-tag>
                  <el-tag type="success" v-else-if="scope.row.questionType === '2'">多选</el-tag>
                  <el-tag type="warning" v-else>简答</el-tag>
                </template>
              </el-table-column>
              <el-table-column class="questionSerial" width="200px" prop="questionIDs[0].number" label="题目编号"></el-table-column>
              <!-- <el-table-column width="200px" label="题目编号">
                <template slot-scope="scope">
                  <el-link @click="parameters(item.id)" type="primary" class="randomsLink" v-for="(item, index) in scope.row.questionIDs" :key="index">{{ item.number }} </el-link>
                </template>
              </el-table-column> -->
              <el-table-column width="200px" prop="addTime" label="录入时间"></el-table-column>
              <el-table-column prop="totalSeconds" label="答题时间"></el-table-column>
              <el-table-column prop="accuracyRate" label="正确率"></el-table-column>
              <el-table-column prop="userName" label="录入人"></el-table-column>
              <el-table-column label="操作">
                <template slot-scope="scope">
                  <el-button type="danger" icon="el-icon-delete" size="mini" circle @click="deleteLIst(scope.row.id)"></el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
          <!-- table表格 -->

          <!-- 分页栏 -->
          <el-pagination
            background
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="queryInfo.page"
            :page-size="queryInfo.pagesize"
            :page-sizes="[20, 30, 50, 100]"
            :total="counts"
            layout=" prev, pager, next,total, sizes, jumper"
          ></el-pagination>
          <!-- 分页栏 -->
          <!-- 弹框 -->
          <el-dialog title="组件详情" :visible.sync="dialogShow" width="65%">
            <!-- 子组件 -->
            <questions-details :dialogID="dialogID"> </questions-details>
            <!-- 子组件 -->
            <span slot="footer" class="dialog-footer" width="100%">
              <el-button class="pvButton" @click="dialogShow = false">关 闭</el-button>
            </span>
          </el-dialog>
          <!-- 弹框 -->
        </el-card>
      </div>
    </div>
  </div>
</template>

<script>
import { randoms, removeRandoms } from '@/api/hmmm/questions.js'
import questionsDetails from '../components/questions-details.vue'

export default {
  data() {
    return {
      // search: '',
      isTableData: [],
      // 查询参数对象
      queryInfo: {
        keyword: '', // 关键字
        page: 1, // 当前页码
        pagesize: 20 // 页大小
      },
      counts: 0, // 总记录条数
      dialogShow: false, // 控制弹窗
      dialogID: null // 传值
    }
  },
  components: {
    questionsDetails
  },
  // 挂载结束

  mounted: function() {},

  created() {
    this.getQuestionData()
    this.wsm()
  },
  methods: {
    async getQuestionData() {
      try {
        const { data } = await randoms(this.queryInfo)
        // console.log(data)
        this.counts = data.counts
        this.queryInfo.pagesize = data.pagesize - 0
        this.isTableData = data.items
        // console.log(this.isTableData)
      } catch (error) {
        this.$message({
          type: 'info',
          message: '获取组题列表失败'
        })
        // console.log(error)
      }
    },
    // 显示页面数
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      // console.log(`每页 ${newSize} 条`)
      this.getQuestionData()
    },
    // 当前页面
    handleCurrentChange(newPage) {
      this.queryInfo.page = newPage
      // console.log(`当前页: ${newPage}`)
      this.getQuestionData()
    },

    // 组题列表删除
    deleteLIst(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          // console.log(id)
          // 删除接口函数
          await removeRandoms({
            id: id
          })
          this.deleteLIstIndex(id)
          this.$message({
            type: 'success',
            message: '删除成功!111'
          })
        })
        .catch(err => {
          console.dir(err)
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
    },

    // 点击删除按钮时处理isTableData数据
    deleteLIstIndex(id) {
      const deleteData = []
      this.isTableData.filter(item => {
        if (item.id !== id) {
          deleteData.push(item)
        }
      })
      this.counts = this.counts - 1
      this.isTableData = deleteData
    },
    // //
    parameters(id) {
      this.dialogShow = true
      this.dialogID = id
    }
  }
}
</script>

<style scoped lang="less">
.container {
  .el-alert {
    margin: 20px 0;
  }
  .Keyword {
    width: 30%;
  }
  .randomsTable {
    font-size: 13px;
    .randomsLink {
      color: rgb(27, 134, 235);
      font-size: 13px;
    }
  }
  .pvButton {
    float: right;
    margin-top: -25px;
  }
}
</style>
