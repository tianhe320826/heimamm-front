<template>
  <div class="add-form">
    <el-dialog :title="text + pageTitle" :visible.sync="dialogFormVisible" width="60%">
      <!-- <el-form label-position="left" label-width="120px" style="width: 400px; margin-left:120px;">
        <el-form-item :label="$t('table.username')" prop="username">
          <el-input v-model="formBase.username"></el-input>
        </el-form-item>
      </el-form> -->
      <h2 v-html="articleInfo.title"></h2>
      <div class="art-text">
        <span>{{ articleInfo.createTime | parseTimeByString() }}</span>
        <span>{{ articleInfo.username }}</span>
        <span class="el-icon-view"></span>
        <span>{{ articleInfo.visits }}</span>
      </div>
      <div class="art-context" v-html="articleInfo.articleBody"></div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'articlesPreview',
  // props: ['text', 'pageTitle', 'PermissionGroupsList', 'formBase', 'ruleInline'],
  props: ['text', 'pageTitle', 'articleInfo'],
  data() {
    return {
      dialogFormVisible: false
      // fileList: [],
      // importFileUrl: 'https://jsonplaceholder.typicode.com/posts/',
    }
  },
  computed: {},
  methods: {
    // 弹层显示
    dialogFormV() {
      this.dialogFormVisible = true
      // this.getArticleInfo()
      console.log(this.articleInfo)
    }
    // 弹层隐藏
    // dialogFormH() {
    //   this.dialogFormVisible = false
    // },
    // 退出
    // handleClose() {
    //   this.$emit('handleCloseModal')
    // },
    // 表单提交
    // createData() {
    //   this.$refs.dataForm.validate(valid => {
    //     if (valid) {
    //       this.$emit('handleCloseModal')
    //       const data = {
    //         ...this.formBase
    //       }
    //       if (this.formBase.id) {
    //         update(data).then(() => {
    //           this.$emit('newDataes', this.formBase)
    //         })
    //       } else {
    //         add(this.formBase).then(() => {
    //           this.$emit('newDataes', this.formBase)
    //         })
    //       }
    //     } else {
    //       this.$Message.error('*号为必填项!')
    //     }
    //   })
    // }
  },
  // 挂载结束

  mounted: function () {},
  // 创建完毕状态
  created() {},
  // 组件更新
  updated: function () {}
}
</script>
<style lang="scss">
.el-form--label-left .el-form-item__label {
  text-align: right;
}
.el-form-item--medium {
  margin-bottom: 22px;
}
.el-dialog__footer {
  text-align: center;
}
.art-text {
  padding: 0 15px 15px;
  span {
    padding: 0 5px;
  }
}
.art-context {
  background: #f5f5f5;
  border-top: 1px dashed #000;
  padding: 15px;
  .ql-align-center {
    width: 100%;
    img {
      max-width: 100% !important;
    }
  }
}
</style>
