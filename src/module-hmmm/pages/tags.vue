<style scoped lang="less"></style>
<template>
  <div class="directorys-container">
    <div class="app-container">
      <el-card shadow="never">
        <el-breadcrumb separator-class="el-icon-arrow-right" v-if="subjecttag">
          <el-breadcrumb-item>学科管理</el-breadcrumb-item>
          <el-breadcrumb-item>{{ subjecttag }}</el-breadcrumb-item>
          <el-breadcrumb-item>标签管理</el-breadcrumb-item>
        </el-breadcrumb>
        <div class="line" v-if="subjecttag"></div>
        <!-- 搜索 -->
        <el-form :model="requestTags" :inline="true">
          <el-form-item :label="$t('table.tagName')" class="tagName">
            <el-input v-model="requestTags.tagName" clearable @keyup.enter.native="getList"></el-input>
          </el-form-item>
          <el-form-item :label="$t('table.state')">
            <el-select v-model="requestTags.state" placeholder="请选择" clearable>
              <el-option :label="$t('table.enable')" :value="1"></el-option>
              <el-option :label="$t('table.prohibit')" :value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button type="defalut" @click="clearForm">{{ $t('table.clear') }}</el-button>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="getList">{{ $t('table.search') }}</el-button>
          </el-form-item>
          <el-form-item class="fr">
            <el-button v-if="subjecttag" type="text" style="margin-left: 10px;" @click="$router.push('/subjects/list')" icon="el-icon-back">返回学科</el-button>
            <el-button size="small" round style="margin-left: 10px;" @click="isAddDialogShow = true" type="success" icon="el-icon-edit">{{ $t('table.addTag') }}</el-button>
          </el-form-item>
        </el-form>
        <!-- 数据总量提示 -->
        <el-alert v-if="alertText !== ''" :title="alertText" type="info" class="alert" :closable="false" show-icon></el-alert>
        <!-- end -->
        <!-- 数据 -->
        <el-table :data="dataList" v-loading="listLoading" element-loading-text="给我一点时间">
          <el-table-column :label="$t('table.id')" width="80" prop="id"></el-table-column>
          <el-table-column :label="$t('table.subjectName')" prop="subjectName"></el-table-column>
          <el-table-column :label="$t('table.tagName')" prop="tagName"></el-table-column>
          <el-table-column :label="$t('table.createdBy')" prop="username"></el-table-column>
          <el-table-column :label="$t('table.creatdate')" :sort-method="changesort" sortable>
            <template slot-scope="scope">
              <span>{{ scope.row.addDate | parseTimeByString }}</span>
            </template>
          </el-table-column>
          <el-table-column :label="$t('table.state')">
            <template slot-scope="scoped">
              <span>{{ scoped.row.state ? '已启用' : '已禁用' }}</span>
            </template>
          </el-table-column>
          <!-- 操作 -->
          <el-table-column align="center" :label="$t('table.actions')" class-name="small-padding fixed-width">
            <template slot-scope="scoped">
              <el-link type="primary" :underline="false" @click="handleChange(scoped.row)">{{ scoped.row.state ? '禁用' : '启用' }}</el-link>
              <el-link :type="scoped.row.state ? 'info' : 'primary'" :underline="false" :disabled="scoped.row.state ? true : false" @click="handleUpdate(scoped.row)">修改</el-link>
              <el-link :type="scoped.row.state ? 'info' : 'primary'" :underline="false" :disabled="scoped.row.state ? true : false" @click="handleRemove(scoped.row)">删除</el-link>
            </template>
          </el-table-column>
        </el-table>
        <!-- end -->
        <!-- 分页 -->
        <div class="pagination fr">
          <PageTool :paginationPage="requestTags.page" :paginationPagesize="requestTags.pagesize" :total="total" @pageChange="handleCurrentChange" @pageSizeChange="handleSizeChange"></PageTool>
        </div>
        <!-- end -->
        <!-- 新增标签弹层 -->
        <tags-add v-if="isAddDialogShow" @close="isAddDialogShow = false" @addTags="addTags" />
        <!-- 编辑标签弹层 -->
        <tags-edit v-if="isEditDialogShow" :directoryObj="directoryObj" @close="isEditDialogShow = false" @EditDirectory="EditDirectorys" />
      </el-card>
    </div>
  </div>
</template>

<script>
import { list, remove, changeState } from '@/api/hmmm/tags'
import PageTool from '../components/pageTool'
import TagsAdd from '../components/tags-add'
import TagsEdit from '../components/tags-edit'

export default {
  name: 'TagsIndex',
  components: {
    TagsAdd,
    TagsEdit,
    PageTool
  },
  data() {
    return {
      total: null,
      // 点击编辑修改的当前行信息
      directoryObj: {},
      // 控制编辑弹窗的显示隐藏
      isEditDialogShow: false,
      // 控制添加弹窗的显示隐藏
      isAddDialogShow: false,
      // 存储表格中信息
      dataList: [],
      // 表单加载loading
      listLoading: false,
      // 数据总数提示文本
      alertText: '',
<<<<<<< HEAD
      subjecttag: this.$route.query.name,
=======
      // 请求数据对象
>>>>>>> 271aa93427de78fe8e0c91be858abd24738fc551
      requestTags: {
        page: 1,
        pagesize: 10,
        tagName: null,
        state: null,
        // 添加搜索ID
        subjectID: this.$route.query.id
      }
    }
  },
  created() {
    this.getList()
  },
  methods: {
    // 获取列表数据
    getList() {
      this.listLoading = true
      if (this.requestTags.state === '' || this.requestTags.tagName === '') {
        this.requestTags.state = null
        this.requestTags.tagName = null
      }
      list(this.requestTags)
        .then(({ data }) => {
          this.dataList = data.items
          this.total = data.counts
          this.alertText = `共 ${data.counts} 条记录`
          this.listLoading = false
        })
        .catch(e => {
          this.$message.e('错了哦，这是一条错误消息')
        })
    },
    // 数据排序
    changesort(a, b) {
      const oldTime = new Date(a.date).getTime() / 1000
      const newime = new Date(b.date).getTime() / 1000
      if (oldTime - newime > 0) {
        return true
      } else {
        return false
      }
    },
    // 清空表单
    clearForm() {
      this.requestTags.tagName = ''
      this.requestTags.state = null
      this.getList()
    },
    // 每页显示信息条数
    handleSizeChange(val) {
      this.requestTags.pagesize = val
      this.getList()
    },
    // 显示的页码
    handleCurrentChange(val) {
      this.requestTags.page = val
      this.getList()
    },
    // 监听添加目录事件，刷新列表
    addTags() {
      this.getList()
      this.isAddDialogShow = false
    },
    // 状态改变事件
    handleChange(row) {
      changeState({
        id: row.id,
        state: row.state ? 0 : 1
      })
        .then(data => {
          row.state = row.state ? 0 : 1
        })
        .catch(e => {
          this.$message.e('错了哦，这是一条错误消息')
        })
    },
    // 编辑
    handleUpdate(row) {
      // this.directoryObj = row
      // this.isEditDialogShow = true
    },
    EditDirectorys() {
      //   this.getList()
      //   this.isEditDialogShow = false
    },
    // 删除
    handleRemove(row) {
      this.$confirm('此操作将永久删除标签' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(() => {
          remove({ id: row.id })
            .then(response => {
              this.$message.success('已成功删除该目录')
              this.getList()
            })
            .catch(response => {
              this.$message.error('删除失败，请稍后重试')
            })
        })
        .catch(() => {
          this.$message.info('已取消该操作')
        })
    }
  }
}
</script>

<style scoped lang="scss">
.el-breadcrumb {
  margin-bottom: 20px;
  font-size: 14px;
}
.line {
  border-bottom: 1px solid #ccc;
  margin-bottom: 12px;
}
.directorys-container {
  .alert {
    margin: 10px 0px;
  }
  .pagination {
    margin: 20px 0;
  }
  .el-link {
    margin-right: 8px;
  }

  .tagName {
    margin-right: 60px;
  }
}
</style>
