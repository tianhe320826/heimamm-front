<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
        <!-- 搜索 -->
        <el-form :model="requestParameters" ref="requestParameters" :inline="true">
          <div class="filter-container">
            <el-form-item label="学科名称">
              <el-input @keyup.enter.native="handleFilter" v-model="requestParameters.subjectName" clearable></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="default" @click="resetForm">{{ $t('table.clear') }}</el-button>
              <el-button type="primary" @click="handleFilter">{{ $t('table.search') }}</el-button>
            </el-form-item>
            <el-button class="fr" round @click="handleCreate" icon="el-icon-edit" type="success">{{ $t('table.addSubjects') }}</el-button>
          </div>
        </el-form>
        <!-- 记录总条数的弹框 -->
        <el-alert v-if="alertText !== ''" :title="alertText" type="info" class="alert" :closable="false" show-icon></el-alert>
        <!-- end -->
        <!-- 数据 -->
        <el-table :data="dataList" v-loading="listLoading" element-loading-text="给我一点时间">
          <el-table-column type="index" width="60" label="序号"> </el-table-column>
          <el-table-column label="学科名称">
            <template slot-scope="scope">
              <span>{{ scope.row.subjectName }}</span>
            </template>
          </el-table-column>
          <el-table-column label="创建者">
            <template slot-scope="scope">
              <span>{{ scope.row.username }}</span>
            </template>
          </el-table-column>
          <el-table-column label="创建日期">
            <template slot-scope="scope">
              <span>{{ scope.row.addDate | parseTimeByString() }}</span>
            </template>
          </el-table-column>
          <el-table-column label="前台是否显示">
            <template slot-scope="scope">
              <span>{{ scope.row.isFrontDisplay ? '是' : '否' }}</span>
            </template>
          </el-table-column>
          <el-table-column label="二级目录">
            <template slot-scope="scope">
              <span>{{ scope.row.twoLevelDirectory }}</span>
            </template>
          </el-table-column>
          <el-table-column label="标签">
            <template slot-scope="scope">
              <span>{{ scope.row.tags }}</span>
            </template>
          </el-table-column>
          <el-table-column label="题目数量">
            <template slot-scope="scope">
              <span>{{ scope.row.totals }}</span>
            </template>
          </el-table-column>
          <!-- 按钮 -->
          <el-table-column label="操作" align="center" min-width="140">
            <template slot-scope="scope">
              <el-button type="text" @click="toCatagory(scope.row)">学科分类</el-button>
              <el-button type="text" @click="toTags(scope.row)">学科标签</el-button>
              <el-button type="text" @click="handleUpdate(scope.row)">修改</el-button>
              <el-button type="text" @click="removeUser(scope.row.id)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- end -->
        <!-- 分页 -->
        <div class="pagination">
          <PageTool
            :paginationPage="requestParameters.page"
            :paginationPagesize="requestParameters.pagesize"
            :total="total"
            @pageChange="handleCurrentChange"
            @pageSizeChange="handleSizeChange"
          ></PageTool>
        </div>
        <!-- end -->
        <!-- 新增标签弹层  -->
        <subjects-add ref="addSubjects" @newDataes="handleLoadDataList" @handleCloseModal="handleCloseModal" @updateSubject="updateSubject"></subjects-add>

        <!-- 编辑弹框 -->
        <el-dialog title="编辑学科" :visible.sync="dialogEditVisible" width="30%">
          <el-form label-position="left" label-width="80px" :model="editForm" :rules="editFormRules" ref="editFormRef">
            <el-form-item label="学科名称" prop="subjectName">
              <el-input v-model="editForm.subjectName"></el-input>
            </el-form-item>
            <el-form-item label="是否显示">
              <el-switch v-model="editForm.isFrontDisplay" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogEditVisible = false">取消</el-button>
            <el-button type="primary" @click="createEdit">确认</el-button>
          </div>
        </el-dialog>
      </el-card>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.alert {
  margin: 10px 0px;
}
.pagination {
  margin-top: 10px;
  text-align: right;
}
</style>

<script>
import { list, remove, update } from '@/api/hmmm/subjects'
import PageTool from '@/module-dashboard/components/pageTool'
import SubjectsAdd from './../components/subjects-add'

export default {
  name: 'base-subject',
  components: {
    SubjectsAdd,
    PageTool
  },
  data() {
    return {
      alertText: '',
      // 用来保存获取的数组
      dataList: [],
      total: null,
      listLoading: false,
      // 编辑弹框
      dialogEditVisible: false,
      editForm: {},
      editFormRules: {
        subjectName: [
          // 表单验证规则
          { required: true, message: '请输入学科名称', trigger: 'blur' }
        ]
      },
      requestParameters: {
        page: 1,
        pagesize: 10,
        subjectName: ''
      }
    }
  },
  created() {
    // 读取数据
    this.getSubjectsList()
  },
  methods: {
    // 获取列表数据
    async getSubjectsList() {
      this.listLoading = true
      const { data } = await list(this.requestParameters)
      // console.log(data)
      try {
        this.dataList = data.items
        this.total = data.counts
        this.alertText = `共 ${this.total} 条记录`
        this.listLoading = false
        // console.log(this.dataList)
      } catch (e) {
        this.$message.e('错了哦，这是一条错误消息')
      }
    },
    // 重置
    resetForm() {
      this.requestParameters.subjectName = ''
      this.getSubjectsList()
    },
    // 子组件传来的更新列表
    updateSubject() {
      this.getSubjectsList()
    },
    // 搜索信息
    handleFilter() {
      this.requestParameters.page = 1
      this.getSubjectsList(this.requestParameters)
    },
    // 每页显示信息条数
    handleSizeChange(val) {
      this.requestParameters.pagesize = val
      if (this.requestParameters.page === 1) {
        this.getSubjectsList(this.requestParameters)
      }
    },
    // 进入某一页
    handleCurrentChange(val) {
      this.requestParameters.page = val
      this.getSubjectsList()
    },
    // 新增学科刷新列表
    handleLoadDataList() {
      this.getSubjectsList()
    },
    // 新增学科
    handleCreate() {
      this.$refs.addSubjects.dialogFormV()
    },
    // 确认编辑按钮
    createEdit() {
      this.$refs.editFormRef.validate(async valid => {
        if (!valid) return false
        await update(this.editForm)
        // console.log(data)
        // 关闭弹框
        this.dialogEditVisible = false
        this.$message.success('编辑成功！')
        // this.editForm = {}
        // 更新学科列表
        this.getSubjectsList()
      })
    },
    // 弹框关闭
    handleCloseModal() {
      this.$refs.addSubjects.dialogFormH()
    },
    // 编辑框获取详情
    async handleUpdate(row) {
      this.editForm = JSON.parse(JSON.stringify(row))
      this.editForm.isFrontDisplay = row.isFrontDisplay && true
      this.dialogEditVisible = true
    },
    // 到学科分类
    toCatagory(params) {
      this.$router.push({
        path: '/subjects/directorys',
        query: {
          id: params.id,
          name: params.subjectName
        }
      })
    },
    // 到学科标签
    toTags(params) {
      this.$router.push({
        path: '/subjects/tags',
        query: {
          id: params.id,
          name: params.subjectName
        }
      })
    },
    // 删除列表
    removeUser(id) {
      this.$confirm('此操作将永久删除该学科 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(() => {
          remove({ id: id })
            .then(response => {
              this.$message.success('成功删除了该学科')
              this.dataList.splice(id, 1)
              this.getSubjectsList()
            })
            .catch(response => {
              this.$message.error('删除失败!')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    }
  }
}
</script>
