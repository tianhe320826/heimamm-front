<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card shadow="never">
        <!-- 搜索 -->
        <el-form :inline="true" :model="requestParameters" ref="requestParameters">
          <el-form-item :label="$t('table.keyword')">
            <el-input clearable @keyup.enter.native="handleFilter" :placeholder="$t('table.searchKeyword')" v-model="requestParameters.keyword"></el-input>
          </el-form-item>
          <el-form-item :label="$t('table.state')">
            <el-select placeholder="请选择" clearable v-model="requestParameters.state">
              <el-option label="启用" value="1"></el-option>
              <el-option label="禁用" value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button @click="resetForm">清空</el-button>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="handleFilter">{{ $t('table.search') }}</el-button>
          </el-form-item>
          <el-button class="fr" @click="handleCreate" type="success" icon="el-icon-edit">{{ $t('table.addSkill') }}</el-button>
        </el-form>
        <el-alert v-if="alertText !== ''" :title="alertText" type="info" class="alert" :closable="false" show-icon></el-alert>
        <!-- end -->
        <!-- 数据 -->
        <el-table :data="dataList" v-loading="listLoading" element-loading-text="给我一点时间">
          <el-table-column align="center" type="index" :label="$t('table.id')" width="60"> </el-table-column>
          <el-table-column align="center" :label="$t('table.articleTitle')" width="400">
            <template slot-scope="scope">
              <span>{{ scope.row.title }}</span>
              <i v-if="scope.row.videoURL" class="el-icon-film" @click="playVideo(scope.row.videoURL)"></i>
            </template>
          </el-table-column>
          <el-table-column align="center" :label="$t('table.reads')" prop="visits"></el-table-column>
          <el-table-column align="center" :label="$t('table.creator')" prop="username"></el-table-column>
          <el-table-column align="center" :label="$t('table.inputtime')">
            <template slot-scope="scope">
              <span>{{ scope.row.createTime | parseTimeByString }}</span>
            </template>
          </el-table-column>
          <el-table-column align="center" :label="$t('table.state')">
            <template slot-scope="scope">
              <span>{{ scope.row.state === 1 ? '已启用' : '已禁用' }}</span>
            </template>
          </el-table-column>
          <!-- 按钮 -->
          <el-table-column align="center" :label="$t('table.actions')" width="240">
            <template slot-scope="scope">
              <el-link :underline="false" type="primary" @click="handlePreview(scope.row)">{{ $t('table.preview') }}</el-link>
              <el-link :underline="false" type="primary" @click="handleUse(scope.row)">{{ $t(scope.row.state ? 'table.unused' : 'table.use') }}</el-link>
              <el-link :underline="false" :type="scope.row.state ? 'info' : 'primary'" :disabled="scope.row.state ? true : false" @click="handleUpdate(scope.row.id)">{{ $t('table.edit') }}</el-link>
              <el-link :underline="false" :type="scope.row.state ? 'info' : 'primary'" :disabled="scope.row.state ? true : false" @click="removeUser(scope.row.id)">{{ $t('table.delete') }}</el-link>
            </template>
          </el-table-column>
        </el-table>
        <!-- 视频 -->
        <div class="video" v-if="showVideo">
          <div class="close-btn" @click="showVideo = false">
            <i class="el-icon-close"></i>
          </div>
          <div class="video-box">
            <video :src="videoURL" autoplay muted controls></video>
          </div>
        </div>
        <!-- 分页 -->
        <div class="pagination fr">
          <PageTool
            :paginationPage="requestParameters.page"
            :paginationPagesize="requestParameters.pagesize"
            :total="total"
            @pageChange="handleCurrentChange"
            @pageSizeChange="handleSizeChange"
          ></PageTool>
        </div>
        <!-- 新增标签弹层 -->
        <component
          v-bind:is="ArticlesAdd"
          :ruleInline="ruleInline"
          :formBase="formData"
          @handleCloseModal="handleCloseModal"
          @newDataes="getList"
          ref="addSkill"
          :text="text"
          :pageTitle="pageTitle"
        ></component>
        <!-- 预览标签弹层 -->
        <articles-preview ref="artPreview" :articleInfo="articleInfo" :text="text" :pageTitle="pageTitle"></articles-preview>
      </el-card>
    </div>
  </div>
</template>

<script>
import { list, remove, changeState, detail } from '@/api/hmmm/articles'
import PageTool from './../components/pageTool'
import ArticlesPreview from './../components/articles-preview'
import ArticlesAdd from './../components/articles-add'
import { validateURL } from '@/utils/validate'
export default {
  name: 'base-users',
  components: {
    ArticlesPreview,
    PageTool,
    ArticlesAdd
  },
  data() {
    var validateUrl = (rule, value, callback) => {
      if (!validateURL(value)) {
        callback(new Error('请输入正确的视频地址'))
      } else {
        callback()
      }
    }
    return {
      articleInfo: {},
      articleId: 0,
      videoURL: '',
      showVideo: false,
      ArticlesAdd: 'ArticlesAdd',
      pageTitle: '文章', // 页面标题
      text: '', // 新增、预览、编辑文本
      dataList: [],
      total: null,
      listLoading: true,
      alertText: '',
      requestParameters: {
        page: 1,
        pagesize: 10,
        keyword: this.keyword,
        state: null
      },
      ruleInline: {
        // 表单验证
        title: [{ required: true, message: '文章标题不能为空', trigger: 'blur' }],
        articleBody: [{ required: true, message: '文章内容不能为空', trigger: 'blur' }],
        videoURL: [{ validator: validateUrl, trigger: 'blur' }]
      },
      formData: {
        title: '',
        articleBody: '',
        videoURL: ''
      }
    }
  },
  methods: {
    // 获取列表数据
    getList(params) {
      this.listLoading = true
      if (this.requestParameters.state === '') {
        this.requestParameters.state = null
      }
      list(params)
        .then(data => {
          this.dataList = data.data.items
          this.total = data.data.counts
          this.alertText = `共 ${this.total} 条记录`
          this.listLoading = false
        })
        .catch(e => {
          this.$message.e('错了哦，这是一条错误消息')
        })
    },
    // 清空
    resetForm() {
      this.requestParameters.keyword = ''
      this.requestParameters.state = null
      this.getList()
    },
    // 搜索信息
    handleFilter() {
      this.requestParameters.page = 1
      this.getList(this.requestParameters)
    },
    // 每页显示信息条数
    handleSizeChange(val) {
      this.requestParameters.pagesize = val
      if (this.requestParameters.page === 1) {
        this.getList(this.requestParameters)
      }
    },
    // 进入某一页
    handleCurrentChange(val) {
      this.requestParameters.page = val
      this.getList(this.requestParameters)
    },
    // 搜索的项目
    query() {
      this.formData = {
        title: '',
        articleBody: '',
        videoURL: ''
      }
    },
    // 新增技巧
    handleCreate() {
      this.query()
      this.text = '新增'
      this.$refs.addSkill.dialogFormV()
    },
    // 弹框关闭
    handleCloseModal() {
      this.$refs.addSkill.dialogFormH()
    },
    // 点击播放视频
    playVideo(url) {
      this.showVideo = true
      this.videoURL = url
    },
    // 编辑
    // 表单详情数据加载
    hanldeEditForm(objeditId) {
      this.formData.id = objeditId
      detail({ id: objeditId }).then((data, err) => {
        var datalist = data.data
        if (err) {
          return err
        }
        this.formData.id = datalist.id
        this.formData.title = datalist.title
        this.formData.articleBody = datalist.articleBody
        this.formData.videoURL = datalist.videoURL
      })
    },
    // 编辑
    handleUpdate(objeditId) {
      this.query()
      this.text = '编辑'
      this.$refs.addSkill.dialogFormV()
      this.hanldeEditForm(objeditId)
    },
    // 删除
    removeUser(user) {
      this.$confirm('此操作将永久删除文章 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(() => {
          remove({ id: user })
            .then(response => {
              this.$message.success('删除成功')
              this.dataList.splice(user, 1)
              this.getList(this.requestParameters)
            })
            .catch(response => {
              this.$message.error('删除失败!')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    },
    // 预览
    handlePreview(info) {
      this.text = '预览'
      this.$refs.artPreview.dialogFormV()
      this.articleInfo = info
    },
    // 启用 禁用
    handleUse(row) {
      const params = {
        id: row.id,
        state: row.state ? 0 : 1
      }
      changeState(params)
        .then(data => {
          this.$message.success('操作成功')
          row.state = row.state ? 0 : 1
          this.getList()
        })
        .catch(data => {
          this.$message.error('操作失败!')
        })
    }
  },
  // 创建完毕状态
  created() {
    // 读取数据
    this.getList(this.requestParameters)
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.alert {
  margin: 10px 0px;
}
.pagination {
  margin: 20px 0;
}
.el-form {
  margin-left: 30px;
}

.disabled .el-button--primary {
  background-color: #dbdada;
  border: 1px solid #ebebeb;
  color: #999;
  cursor: not-allowed;
}
.el-link {
  padding: 5px;
}
.el-icon-film {
  padding: 0 5px;
  color: #0000ff;
  font-size: 18px;
}
.video {
  background: rgba(0, 0, 0, 0.3);
  width: 100%;
  height: 100%;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 9999;
}
.close-btn {
  width: 50px;
  height: 50px;
  position: absolute;
  top: 30px;
  left: 50%;
  transform: translate(-50%);
  background: rgba(0, 0, 0, 0.4);
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.4);
  border-radius: 50%;
  text-align: center;
  line-height: 50px;
  color: #fff;
  font-size: 20px;
  cursor: pointer;
}
.video-box {
  width: 800px;
  height: 450px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.video-box video {
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
}
</style>
