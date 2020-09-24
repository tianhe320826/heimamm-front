
<style scoped lang='less'></style>
<template>
  <div class="container">
    <div class="dashboard-container">
      <div class="app-container">
        <el-card shadow="never">
          <div class="topSearch" style="width: 100%">
            <el-form :inline="true" ref="form" :model="queryInfo">
              <div>
                <el-form-item label="标签名称">
                  <el-input v-model="queryInfo.tagName" size="mini"></el-input>
                </el-form-item>
                <el-form-item label="状态">
                  <el-select v-model="queryInfo.state" placeholder="请选择">
                    <el-option label="启用" value="1"></el-option>
                    <el-option label="禁用" value="0"></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item>
                  <el-button size="mini" type="info">清除</el-button>
                  <el-button size="mini" type="primary" @click="getTagData">搜索</el-button>
                </el-form-item>
              </div>
            </el-form>
            <el-button size="mini" icon="el-icon-edit" type="success" @click="tagDialogTableVisible = true">新增学科</el-button>
          </div>

          <!-- 顶部栏 -->

          <!-- 提示 -->
          <el-alert :title="`总共${counts}条数据!`" type="info" :closable="false" show-icon></el-alert>
          <!-- 提示 -->

          <!-- table表格 -->
          <el-table :data="tagData" style="width: 100%">
            <el-table-column prop="id" label="序号" width="180"> </el-table-column>
            <el-table-column prop="subjectName" label="所属学科" width="180"> </el-table-column>
            <el-table-column prop="tagName" label="标签名称"> </el-table-column>
            <el-table-column prop="username" label="创建者"> </el-table-column>
            <el-table-column prop="addDate" label="创建日期"> </el-table-column>
            <el-table-column prop="state" label="状态">
              <template slot-scope="scope">
                <span> {{ scope.row.state == 1 ? '已启用' : '已禁用' }}</span>
              </template>
            </el-table-column>
            <el-table-column label="操作" min-width="150px">
              <template slot-scope="">
                <div class="operation">
                  <span>启用</span>
                  <span>修改</span>
                  <span>删除</span>
                </div>
              </template>
            </el-table-column>
          </el-table>
          <!-- table表格 -->

          <!-- 分页栏 -->
          <el-pagination
            background
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="queryInfo.page"
            :page-size="queryInfo.pagesize"
            :page-sizes="[10, 20, 30, 50]"
            :total="counts"
            layout=" prev, pager, next,total, sizes, jumper"
          ></el-pagination>
          <!-- 分页栏 -->

          <!-- 添加弹框 -->
          <template>
            <el-dialog title="新增目录" :visible.sync="tagDialogTableVisible">
              <el-form :model="form">
                <el-form-item label="活动区域" :label-width="formLabelWidth">
                  <el-select v-model="form.region" placeholder="请选择活动区域">
                    <el-option label="区域一" value="shanghai"></el-option>
                    <el-option label="区域二" value="beijing"></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="活动名称" :label-width="formLabelWidth">
                  <el-input v-model="form.name" autocomplete="off"></el-input>
                </el-form-item>
              </el-form>
              <div slot="footer" class="dialog-footer">
                <el-button @click="tagDialogTableVisible = false">取 消</el-button>
                <el-button type="primary" @click="tagDialogTableVisible = false">确 定</el-button>
              </div>
            </el-dialog>
          </template>

          <!-- 添加弹框 -->
        </el-card>
      </div>
    </div>
  </div>
</template>

<script>
import { list } from '@/api/hmmm/tags.js'
// import { pluralize } from '@/src/filters/index.js'
export default {
  data() {
    return {
      counts: 0, // 数据总条数
      // 请求参数
      queryInfo: {
        page: 1,
        pagesize: 10,
        subjectID: '', // 学科ID
        tagName: '',
        state: null
      },
      tagData: [],
      // //--------------
      gridData: [
        {
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        },
        {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        },
        {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        },
        {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }
      ],
      tagDialogTableVisible: false,
      tagDialogFormVisible: false,
      form: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      },
      formLabelWidth: '120px'
    }
  },
  created() {
    this.getTagData()
  },
  mounted() {},
  methods: {
    async getTagData() {
      try {
        const { data } = await list({
          page: this.queryInfo.page,
          pagesize: this.queryInfo.pagesize,
          subjectID: this.queryInfo.ssubjectID,
          tagName: this.queryInfo.tagName,
          state: this.queryInfo.state
        })
        this.tagData = data.items
        this.counts = data.counts
        // console.log(data)
      } catch (error) {
        console.log(error)
      }
    },
    // 显示页面数
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      // console.log(`每页 ${newSize} 条`)
      this.getTagData()
    },
    // 当前页面
    handleCurrentChange(newPage) {
      this.queryInfo.page = newPage
      console.log(`当前页: ${newPage}`)
      this.getTagData()
    },
    //  添加 弹框
    addTagData() {
      this.$alert('<strong>这是 <i>HTML</i> 片段</strong>', '新增目录', {
        dangerouslyUseHTMLString: true
      })
    }
  }
}
</script>

<style scoped lang='less'>
.container {
  .topSearch {
    display: flex;
    justify-content: space-between;
  }
  .operation {
    // background: aquamarine;
    color: rgb(18, 118, 233);
    font-size: 13px;
    span {
      margin: 0 5px;
    }
  }
  .el-form-item {
    margin-right: 30px;
  }
  .el-button--success {
    height: 28px;
  }
  .el-alert {
    margin-bottom: 20px;
  }
}
</style>
