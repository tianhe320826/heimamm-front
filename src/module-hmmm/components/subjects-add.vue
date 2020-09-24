<template>
  <div class="add-form">
    <el-dialog :title="text + pageTitle" :visible.sync="dialogFormVisible">
      <el-form label-position="left" label-width="120px" style="width: 400px; margin-left:120px;">
        <el-form-item label="学科名称" prop="username">
          <el-input placeholder="请输入学科名称"></el-input>
        </el-form-item>
        <el-form-item :label="是否显示">
          <el-switch v-model="form.delivery"></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="handleClose">取消</el-button>
        <el-button type="primary" @click="createData">确认</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
// import { remove, update, add } from '@/api/hmmm/subjects'
// import { add } from '@/api/hmmm/subjects'
export default {
  name: 'subjectssAdd',
  props: ['pageTitle'],
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
    },
    // 弹层隐藏
    dialogFormH() {
      this.dialogFormVisible = false
    },
    // 退出
    handleClose() {
      this.$emit('handleCloseModal')
    }

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

  mounted: function() {},
  // 创建完毕状态
  created() {}
  // 组件更新
  // updated: function() {}
}
</script>
<style lang="scss" scoped>
.el-form--label-left .el-form-item__label {
  text-align: right;
}
.el-form-item--medium {
  margin-bottom: 22px;
}
.el-dialog__footer {
  text-align: center;
}
</style>
