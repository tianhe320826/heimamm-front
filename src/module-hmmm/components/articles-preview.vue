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
      // console.log(this.articleInfo)
    }
  }
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
