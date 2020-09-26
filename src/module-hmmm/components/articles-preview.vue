<template>
  <div class="add-form">
    <el-dialog :title="text + pageTitle" :visible.sync="dialogFormVisible" width="60%">
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
  props: ['text', 'pageTitle', 'articleInfo'],
  data() {
    return {
      dialogFormVisible: false
    }
  },
  computed: {},
  methods: {
    // 弹层显示
    dialogFormV() {
      this.dialogFormVisible = true
      console.log(this.articleInfo)
    }
<<<<<<< HEAD
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
=======
  }
>>>>>>> 8a84d5b61c63c27b7c0b4d365094ed0e5f72e91a
}
</script>
<style lang="scss">
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
