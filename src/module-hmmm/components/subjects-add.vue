<template>
  <el-dialog title="新增学科" :visible.sync="dialogFormVisible" width="30%" @close="dialogFormH">
    <el-form label-width="80px" :model="addForm" :rules="addFormRules" ref="addFormRef">
      <el-form-item label="学科名称" prop="subjectName">
        <el-input placeholder="请输入学科名称" v-model="addForm.subjectName" @keyup.enter.native="createData"></el-input>
      </el-form-item>
      <el-form-item label="是否显示">
        <el-switch v-model="addForm.isFrontDisplay" active-color="#13ce66" inactive-color="#ff4949"></el-switch>
      </el-form-item>
    </el-form>
    <div slot="footer">
      <el-button @click="$emit('close')">取消</el-button>
      <el-button type="primary" @click="createData">确认</el-button>
    </div>
  </el-dialog>
</template>

<script>
import { add } from '@/api/hmmm/subjects'
export default {
  name: 'subjectssAdd',
  data() {
    return {
      addForm: {
        subjectName: '',
        isFrontDisplay: true
      },
      dialogFormVisible: false,
      addFormRules: {
        subjectName: [
          // 表单验证规则
          { required: true, message: '请输入学科名称', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    // 弹层显示
    dialogFormV() {
      this.dialogFormVisible = true
    },
    // 弹层隐藏
    dialogFormH() {
      this.dialogFormVisible = false
      this.$refs.addFormRef.resetFields()
    },
    // 确定提交
    createData() {
      this.$refs.addFormRef.validate(async (valid) => {
        if (!valid) return false
        await add(this.addForm)
        // 让父组件关闭弹框
        this.$emit('close')
        this.$message.success('添加学科成功！')
        this.addForm = {}
        // 让父组件更新学科列表
        this.$emit('updateSubject')
      })
    }
  }
}
</script>
<style lang="scss" scoped></style>
