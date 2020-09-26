<template>
  <div class="questions-check">
    <el-dialog title="试题审核" width="400px" :visible.sync="checkDialogVisible">
      <el-form>
        <el-radio-group v-model="checkForm.chkState">
          <el-radio :label="1">通过</el-radio>
          <el-radio :label="2">拒绝</el-radio>
        </el-radio-group>
        <br />
        <el-input type="textarea" placeholder="请输入审核意见" required style="width: 100%; margin: 20px 0 20px 0" v-model="checkForm.chkRemarks"> </el-input>
      </el-form>
      <div slot="footer" class="dialog-footer right-btn">
        <el-button @click="handleClose">取消</el-button>
        <el-button type="primary" @click="handleChoiceCheck">确认</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { choiceCheck } from '@/api/hmmm/questions'
export default {
  data() {
    return {
      // 控制审核对话框显示
      checkDialogVisible: false
    }
  },
  props: ['checkForm'],
  methods: {
    // 弹层显示
    dialogFormV() {
      this.checkDialogVisible = true
    },
    // 弹层隐藏
    dialogFormH() {
      this.checkDialogVisible = false
    },
    // 退出
    handleClose() {
      this.$emit('handleCloseModal')
      this.checkForm.chkRemarks = ''
    },
    async handleChoiceCheck() {
      if (!this.checkForm.chkRemarks.trim()) {
        return this.$message.warning('请输入审核意见')
      }
      try {
        await choiceCheck(this.checkForm)
        this.checkForm.chkRemarks = ''
        this.$emit('newDataes')
        this.$message.success('审核成功')
      } catch (e) {
        this.$message.error('审核失败')
      }
      this.dialogFormH()
    }
  }
}
</script>

<style>
.right-btn {
  display: flex;
  justify-content: flex-end;
}
</style>
