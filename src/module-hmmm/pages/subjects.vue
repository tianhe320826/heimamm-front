<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card shadow="never">
        <!-- 搜索 -->
        <el-form :model="requestParameters" ref="requestParameters" :inline="true">
          <div class="filter-container">
            <el-form-item label="学科名称">
              <el-input @keyup.enter="handleFilter" style="width: 200px;" class="filter-item" v-model="requestParameters.subjectName"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button class="filter-item" size="small" type="default" @click="resetForm">{{ $t('table.clear') }}</el-button>
              <el-button class="filter-item" size="small" type="primary" @click="handleFilter">{{ $t('table.search') }}</el-button>
            </el-form-item>
            <el-button class="filter-item fr" size="small" style="margin-left: 10px;" @click="handleCreate" icon="el-icon-edit" type="success">{{ $t('table.addSubjects') }}</el-button>
          </div>
        </el-form>
        <!-- 记录总条数的弹框 -->
        <el-alert v-if="alertText !== ''" :title="alertText" type="info" class="alert" :closable="false" show-icon></el-alert>
        <!-- end -->
        <!-- 数据 -->
        <el-table :key="tableKey" :data="dataList" :row-class-name="rowClassStatus" v-loading="listLoading" element-loading-text="给我一点时间" fit highlight-current-row style="width: 100%" border>
          <el-table-column type="index" width="50" label="序号"> </el-table-column>
          <el-table-column align="center" width="200" label="学科名称">
            <template slot-scope="scope">
              <span>{{ scope.row.subjectName }}</span>
            </template>
          </el-table-column>
          <el-table-column align="center" width="200" label="创建者">
            <template slot-scope="scope">
              <span>{{ scope.row.username }}</span>
            </template>
          </el-table-column>
          <el-table-column align="center" width="300" label="创建日期">
            <template slot-scope="scope">
              <span>{{ scope.row.addDate | parseTimeByString() }}</span>
            </template>
          </el-table-column>
          <el-table-column width="120" label="前台是否显示">
            <template slot-scope="scope">
              <span>{{ scope.row.isFrontDisplay ? '是' : '否' }}</span>
            </template>
          </el-table-column>
          <el-table-column width="120" label="二级目录">
            <template slot-scope="scope">
              <span>{{ scope.row.twoLevelDirectory }}</span>
            </template>
          </el-table-column>
          <el-table-column width="120" label="标签">
            <template slot-scope="scope">
              <span>{{ scope.row.tags }}</span>
            </template>
          </el-table-column>
          <el-table-column width="120" label="题目数量">
            <template slot-scope="scope">
              <span>{{ scope.row.totals }}</span>
            </template>
          </el-table-column>
          <!-- 按钮 -->
          <el-table-column align="center" label="操作" class-name="small-padding fixed-width">
            <template slot-scope="scope">
              <el-button type="text" size="mini" @click="handleUpdate(scope.row.id)">学科分类</el-button>
              <el-button type="text" size="mini" @click="handleUpdate(scope.row.id)">学科标签</el-button>
              <el-button type="text" size="mini" @click="handleUpdate(scope.row.id)">修改</el-button>
              <el-button type="text" size="mini" @click="removeUser(scope.row.id)">删除</el-button>
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
        <!-- 新增标签弹层 -->
        <component
          v-bind:is="SubjectsAdd"
          ref="editUser"
          :formData.sync="requestParameters"
          :text="text"
          :pageTitle="pageTitle"
          :formBase="formData"
          :ruleInline="ruleInline"
          :PermissionGroupsList="PermissionGroupsList"
          v-on:newDataes="handleLoadDataList"
          v-on:handleCloseModal="handleCloseModal"
        ></component>
      </el-card>
    </div>
  </div>
</template>
<style rel="stylesheet/scss" lang="scss" scoped>
.alert {
  margin: 10px 0px;
}
.pagination {
  margin-top: 10px;
  // text-align: right;
}
</style>

<style>
.el-table th {
  background-color: #fafafa;
}
.el-table th.is-leaf {
  border-bottom: 2px solid #e8e8e8;
}
/* .el-form-item {
  margin-bottom: 0;
} */
.disabled td {
  background-color: #f9f9f9;
  color: #c1c1c1;
}
.disabled .el-button--primary,
.disabled .el-button--danger {
  background-color: #dbdada;
  border: 1px solid #dbdada;
  color: #999;
  cursor: not-allowed;
}
</style>
<script>
// import { simple } from '@/api/base/permissions'
import { list } from '@/api/hmmm/subjects'
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
      pageTitle: '用户', // 页面标题
      text: '', // 新增、编辑文本
      tableKey: 0,
      deletedDate: false,
      showDate: true,
      alertText: '',
      // 用来保存获取的数组
      dataList: [],
      total: null,
      listLoading: false,
      requestParameters: {
        page: 1,
        pagesize: 10
      },
      subjectName: '',
      formData: {
        email: '',
        phone: '',
        username: '',
        password: '',
        role: '',
        permission_group_id: '',
        permission_group_title: '',
        avatar: '',
        introduction: ''
      }
    }
  },
  computed: {},
  methods: {
    // 获取列表数据
    async getSubjectsList(params) {
      this.listLoading = false
      const { data } = await list(this.requestParameters)
      console.log(data)
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
    // 权限列表
    // setupData() {
    //   simple().then(data => {
    //     this.PermissionGroupsList = data.data
    //   })
    // },
    // 重置
    resetForm() {
      // this.$refs.requestParameters.resetFields()
      this.requestParameters.subjectName = ''
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
    // 新增用户刷新列表
    // handleLoadDataList() {
    //   this.getList()
    // },
    // 数据删除后显示样式
    rowClassStatus(row) {
      // if (row.row.is_deleted === 1) {
      //   this.deletedDate = true
      //   this.showDate = false
      //   return 'disabled'
      // } else {
      //   return ''
      // }
    },
    // **********************************
    // 搜索的项目
    query() {
      this.formData = {
        email: '',
        phone: '',
        username: '',
        role: '',
        password: '',
        permission_group_id: '',
        avatar: '',
        introduction: ''
      }
    },
    // 新增学科
    handleCreate() {
      this.$refs.editUser.dialogFormV()
    },
    // 窗口操作**********************************
    // 弹框关闭
    handleCloseModal() {
      this.$refs.editUser.dialogFormH()
    },
    // 编辑
    // 表单详情数据加载
    hanldeEditForm(objeditId) {},
    handleUpdate(objeditId) {
      this.query()
      var _this = this
      this.text = '编辑'
      this.$refs.editUser.dialogFormV()
      _this.hanldeEditForm(objeditId)
    },
    // 删除
    removeUser(user) {
      // this.$confirm('此操作将永久删除用户 ' + ', 是否继续?', '提示', {
      //   type: 'warning'
      // })
      //   .then(() => {
      //     remove({ id: user })
      //       .then(response => {
      //         this.$message.success('成功删除了用户' + '!')
      //         this.dataList.splice(user, 1)
      //         this.getList(this.requestParameters)
      //       })
      //       .catch(response => {
      //         this.$message.error('删除失败!')
      //       })
      //   })
      //   .catch(() => {
      //     this.$message.info('已取消操作!')
      //   })
    }
  },
  // 挂载结束
  mounted: function() {},
  // 创建完毕状态
  created() {
    // 读取数据
    this.getSubjectsList()
  },
  // 组件更新
  updated: function() {}
}
</script>
