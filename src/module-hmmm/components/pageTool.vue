<template>
  <div class="pages">
<<<<<<< HEAD
      <el-pagination background
=======
    <el-pagination
      background
>>>>>>> b4e37615ec39115664e43e42448da16c0a8dfb11
      @size-change="onPageSizeChange"
      @current-change="onPageChange"
      :current-page="Number(paginationPage)"
      :total="Number(total)"
      :page-size="Number(paginationPagesize)"
<<<<<<< HEAD
      :page-sizes="[10,20,30, 50]"
      layout="sizes, prev, pager, next, jumper"
      >
      </el-pagination>
=======
      :page-sizes="[5,10,20,30]"
      layout="total, sizes, prev, pager, next, jumper"
    ></el-pagination>
>>>>>>> b4e37615ec39115664e43e42448da16c0a8dfb11
  </div>
</template>
<script>
export default {
  name: 'PageTool',
  props: ['total', 'paginationPage', 'paginationPagesize'],
<<<<<<< HEAD
  data () {
    return {
    }
=======
  data() {
    return {}
>>>>>>> b4e37615ec39115664e43e42448da16c0a8dfb11
  },
  methods: {
    // 进入某一页
    onPageChange: function (pageNum) {
      this.$emit('pageChange', pageNum)
    },
    // 每页显示信息条数
    onPageSizeChange: function (pageSize) {
      this.$emit('pageSizeChange', pageSize)
    }
  }
}
</script>
<style>
<<<<<<< HEAD
.blue{color: #409EFF;}
=======
.blue {
  color: #409eff;
}
>>>>>>> b4e37615ec39115664e43e42448da16c0a8dfb11
</style>
