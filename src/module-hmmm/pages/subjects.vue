<template>
  <div class="container">学科管理</div>
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

<style lang="scss" scoped>
.el-table th {
  background-color: #fafafa;
}
.el-table th.is-leaf {
  border-bottom: 2px solid #e8e8e8;
}
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
    async getSubjectsList(params) {
      this.listLoading = false
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
    // 窗口操作**********************************
    // 弹框关闭
    handleCloseModal() {
      this.$refs.addSubjects.dialogFormH()
    },
    // 编辑框获取详情
    async handleUpdate(row) {
      this.editForm = JSON.parse(JSON.stringify(row))
      this.editForm.isFrontDisplay = row.isFrontDisplay === 1 ? true : false
      this.dialogEditVisible = true
    },
    // 到学科分类
    toCatagory(params) {
      // console.log(111)
      this.$router.push({
        path: '/subjects/directorys/',
        query: {
          id: params.id,
          name: params.subjectName
        }
      })
    },
    // 到学科标签
    toLabel(params) {
      // console.log(row)

      this.$router.push({
        path: '/subjects/tags/',
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
