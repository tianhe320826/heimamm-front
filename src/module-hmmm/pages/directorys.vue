<template>
  <div class="directorys-container">
    <div class="app-container">
      <el-card shadow="never">
        <el-breadcrumb separator-class="el-icon-arrow-right" v-if="subjectdir">
          <el-breadcrumb-item>学科管理</el-breadcrumb-item>
          <el-breadcrumb-item>{{ subjectdir }}</el-breadcrumb-item>
          <el-breadcrumb-item>目录管理</el-breadcrumb-item>
        </el-breadcrumb>
        <div class="line" v-if="subjectdir"></div>
        <!-- 搜索 -->
        <el-form :model="requestDirectory" ref="requestDirectoryRef" :inline="true">
          <el-form-item :label="$t('table.directoryName')" class="directoryName">
            <el-input v-model="requestDirectory.directoryName" clearable @keyup.enter.native="getList"></el-input>
          </el-form-item>
          <el-form-item :label="$t('table.state')">
            <el-select v-model="requestDirectory.state" placeholder="请选择" clearable>
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
            <el-button v-if="subjectdir" type="text" style="margin-left: 10px;" @click="$router.push('/subjects/list')" icon="el-icon-back">返回学科</el-button>
            <el-button size="small" round style="margin-left: 10px;" @click="isAddDialogShow = true" type="success" icon="el-icon-edit">{{ $t('table.addDirectory') }}</el-button>
          </el-form-item>
        </el-form>
        <el-alert v-if="alertText !== ''" :title="alertText" type="info" class="alert" :closable="false" show-icon></el-alert>
        <!-- end -->
        <!-- 数据 -->
        <el-table :key="tableKey" :data="dataList" v-loading="listLoading" element-loading-text="给我一点时间" fit highlight-current-row style="width: 100%">
          <el-table-column :label="$t('table.id')" width="80" prop="id"></el-table-column>
          <el-table-column :label="$t('table.subjectName')" prop="subjectName"></el-table-column>
          <el-table-column :label="$t('table.directoryName')" prop="directoryName"></el-table-column>
          <el-table-column :label="$t('table.createdBy')" prop="username"></el-table-column>
          <el-table-column :label="$t('table.creatdate')" :sort-method="changesort" sortable>
            <template slot-scope="scope">
              <span>{{ scope.row.addDate | parseTimeByString }}</span>
            </template>
          </el-table-column>
          <el-table-column :label="$t('table.QuestionsNum')" prop="totals"></el-table-column>
          <el-table-column :label="$t('table.state')">
            <template slot-scope="scoped">
              <span>{{ scoped.row.state ? '已启用' : '已禁用' }}</span>
            </template>
          </el-table-column>
          <!-- 操作 -->
          <el-table-column align="center" :label="$t('table.actions')">
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
          <PageTool
            :paginationPage="requestDirectory.page"
            :paginationPagesize="requestDirectory.pagesize"
            :total="total"
            @pageChange="handleCurrentChange"
            @pageSizeChange="handleSizeChange"
          ></PageTool>
        </div>
        <!-- end -->
        <!-- 新增目录弹层 -->
        <directorys-add v-if="isAddDialogShow" @close="isAddDialogShow = false" @addDirectory="addDirectorys" />
        <!-- 编辑目录弹层 -->
        <directorys-edit v-if="isEditDialogShow" :directoryObj="directoryObj" @close="isEditDialogShow = false" @EditDirectory="EditDirectorys" />
      </el-card>
    </div>
  </div>
</template>

<script>
import { list, remove, changeState } from '@/api/hmmm/directorys'
import PageTool from '../components/pageTool'
import DirectorysAdd from '../components/directorys-add'
import DirectorysEdit from '../components/directorys-edit'

export default {
  name: 'DirectoryIndex',
  components: {
    DirectorysAdd,
    DirectorysEdit,
    PageTool
  },
  data() {
    return {
      // isShow: false,
      directoryObj: {},
      isEditDialogShow: false,
      isAddDialogShow: false,
      text: '', // 新增、编辑文本
      tableKey: 0,
      dataList: [],
      total: null,
      listLoading: false,
      alertText: '',
      subjectdir: this.$route.query.name,
      requestDirectory: {
        page: 1,
        pagesize: 10,
        directoryName: null,
        subjectID: this.$route.query.id,
        state: null
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
      if (this.requestDirectory.state === '' || this.requestDirectory.requestDirectory === '') {
        this.requestDirectory.state = null
        this.requestDirectory.requestDirectory = null
      }
      list(this.requestDirectory)
        .then(({ data }) => {
          this.dataList = data.items
          this.total = data.counts
          this.alertText = `共 ${this.total} 条记录`
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
      this.requestDirectory.directoryName = ''
      this.requestDirectory.state = null
      this.getList()
    },
    // 每页显示信息条数
    handleSizeChange(val) {
      this.requestDirectory.pagesize = val
      this.getList()
    },
    // 显示的页码
    handleCurrentChange(val) {
      this.requestDirectory.page = val
      this.getList()
    },
    // 监听添加目录事件，刷新列表
    addDirectorys() {
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
      this.directoryObj = row
      this.isEditDialogShow = true
    },
    EditDirectorys() {
      this.getList()
      this.isEditDialogShow = false
    },
    // 删除
    handleRemove(row) {
      this.$confirm('此操作将永久删除用户 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(() => {
          remove({ id: row.id })
            .then(response => {
              this.$message.success('已成功删除目录！')
              this.getList()
            })
            .catch(response => {
              this.$message.error('删除失败！')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    }
  }
}
</script>

<style scoped lang="scss">
.directorys-container {
  .el-breadcrumb {
    margin-bottom: 20px;
    font-size: 14px;
  }
  .line {
    border-bottom: 1px solid #ccc;
    margin-bottom: 12px;
  }
  // 数据条数提示条
  .alert {
    margin: 10px 0px;
  }
  // 底部分页区域
  .pagination {
    margin: 20px 0;
  }
  // 右侧点击文字
  .el-link {
    margin-right: 8px;
  }
  // 目录名称输入框
  .directoryName {
    margin-right: 60px;
  }
}
</style>
