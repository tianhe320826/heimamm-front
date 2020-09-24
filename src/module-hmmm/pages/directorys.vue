<template>
  <div class="directorys-container">
    <div class="app-container">
      <el-card shadow="never">
        <!-- 搜索 -->
        <el-form :model="requestDirectory" ref="requestDirectoryRef" :inline="true">
          <el-form-item :label="$t('table.directoryName')" class="directoryName">
            <el-input
              v-model="requestDirectory.directoryName"
              clearable
              @keyup.enter.native="getList"
            ></el-input>
          </el-form-item>
          <el-form-item :label="$t('table.state')">
            <el-select v-model="requestDirectory.state" placeholder="请选择" clearable>
              <el-option :label="$t('table.enable')" :value="1"></el-option>
              <el-option :label="$t('table.prohibit')" :value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button type="defalut" @click="clearForm">{{$t('table.clear')}}</el-button>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="getList">{{$t('table.search')}}</el-button>
          </el-form-item>
          <el-form-item class="fr">
            <el-button
              size="small"
              round
              style="margin-left: 10px;"
              @click="isAddDialogShow=true"
              type="success"
              icon="el-icon-edit"
            >{{$t('table.addDirectory')}}</el-button>
          </el-form-item>
        </el-form>
        <el-alert
          v-if="alertText !== ''"
          :title="alertText"
          type="info"
          class="alert"
          :closable="false"
          show-icon
        ></el-alert>
        <!-- end -->
        <!-- 数据 -->
        <el-table
          :key="tableKey"
          :data="dataList"
          v-loading="listLoading"
          element-loading-text="给我一点时间"
          fit
          highlight-current-row
          style="width: 100%"
        >
          <el-table-column :label="$t('table.id')" width="80" prop="id"></el-table-column>
          <el-table-column :label="$t('table.subjectName')" prop="subjectName"></el-table-column>
          <el-table-column :label="$t('table.directoryName')" prop="directoryName"></el-table-column>
          <el-table-column :label="$t('table.createdBy')" prop="username"></el-table-column>
          <el-table-column :label="$t('table.creatdate')" :sort-method="changesort" sortable>
            <template slot-scope="scope">
              <span>{{scope.row.addDate | parseTimeByString}}</span>
            </template>
          </el-table-column>
          <el-table-column :label="$t('table.QuestionsNum')" prop="totals"></el-table-column>
          <el-table-column :label="$t('table.state')">
            <template slot-scope="scoped">
              <span>{{ scoped.row.state ? '已启用':'已禁用'}}</span>
            </template>
          </el-table-column>
          <!-- 操作 -->
          <el-table-column
            align="center"
            :label="$t('table.actions')"
            class-name="small-padding fixed-width"
          >
            <template slot-scope="scoped">
              <el-link
                type="primary"
                :underline="false"
                @click="change(scoped.row)"
              >{{scoped.row.state ? '禁用':'启用'}}</el-link>
              <el-link type="primary" :underline="false" :disabled="scoped.row.state?false:true">修改</el-link>
              <el-link type="primary" :underline="false" :disabled="scoped.row.state?false:true">删除</el-link>
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
        <directorys-add
          v-if="isAddDialogShow"
          @close="isAddDialogShow=false"
          @addDirectory="addDirectorys"
        ></directorys-add>
      </el-card>
    </div>
  </div>
</template>

<script>
import { list, remove, detail, update } from '@/api/hmmm/directorys'
import PageTool from '../components/pageTool'
import DirectorysAdd from '../components/directorys-add'
export default {
  name: 'base-permissions',
  components: {
    DirectorysAdd,
    PageTool
  },
  data() {
    return {
      isAddDialogShow: false,
      text: '', // 新增、编辑文本
      tableKey: 0,
      dataList: [],
      total: null,
      listLoading: false,
      alertText: '',
      requestDirectory: {
        page: 1,
        pagesize: 10,
        requestDirectory: null,
        state: null
      }
    }
  },
  computed: {},
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
        .catch((e) => {
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
    change(val) {
      val.state = !val.state
    },
    // 窗口操作**********************************
    // 编辑
    handleUpdate(objeditId) {},
    // 删除
    removeUser(user) {
      // this.$confirm('此操作将永久删除用户 ' + ', 是否继续?', '提示', {
      //   type: 'warning'
      // })
      //   .then(() => {
      //     remove({ id: user })
      //       .then((response) => {
      //         this.$message.success('成功删除了用户' + '!')
      //         this.dataList.splice(user, 1)
      //         this.getList(this.requestParameters)
      //       })
      //       .catch((response) => {
      //         this.$message.error('删除失败!')
      //       })
      //   })
      //   .catch(() => {
      //     this.$message.info('已取消操作!')
      //   })
    }
  },
  // 挂载结束
  mounted: function () {},
  // 创建完毕状态
  created() {
    this.getList()
  }
}
</script>

<style scoped lang='scss'>
.directorys-container {
  padding: 10px;
  .alert {
    margin: 10px 0px;
  }
  .pagination {
    margin: 20px 0;
  }
  .el-link {
    margin-right: 20px;
  }

  .el-table th {
    background-color: #fafafa;
  }
  .el-table th.is-leaf {
    border-bottom: 2px solid #e8e8e8;
  }
  .directoryName {
    margin-right: 60px;
  }
}
</style>
