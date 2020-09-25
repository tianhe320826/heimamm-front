<template>
  <el-dialog title="修改目录" :visible.sync="EditDialogVisible" width="30%" @close="$emit('close', false)">
    <el-form :model="editObject" :rules="rules" ref="directoryRef" label-width="80px">
      <el-form-item :label="$t('table.subjectName')">
        <el-select v-model="editObject.subjectID" placeholder="请选择" clearable>
          <el-option v-for="item in list" :key="item.value" :label="item.label" :value="item.value" />
        </el-select>
      </el-form-item>
      <el-form-item :label="$t('table.directoryName')" prop="directoryName">
        <el-input v-model.trim="editObject.directoryName" placeholder="请输入目录名称" @keyup.enter.native="EditDirectory"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer">
      <el-button @click="$emit('close', false)">{{ $t('table.cancel') }}</el-button>
      <el-button type="primary" @click="EditDirectory">{{ $t('table.confirm') }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { update } from '@/api/hmmm/directorys'
import { simple } from '@/api/hmmm/subjects'

export default {
  name: 'DirectoryEdit',
  props: {
    directoryObj: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      // 当前编辑项信息
      editObject: JSON.parse(JSON.stringify(this.directoryObj)),
      // 控制添加弹层的显示与隐藏
      EditDialogVisible: true,
      // 存储学科列表
      list: [],
      // 编辑目录验证规则
      rules: {
        directoryName: [{ required: true, message: '请输入目录名称', trigger: 'blur' }]
      }
    }
  },
  created() {
    this.getSubjectList()
  },
  methods: {
    // 获取学科列表
    getSubjectList() {
      simple()
        .then(({ data }) => {
          this.list = data
        })
        .catch(e => {
          this.$message('获取学科列表失败')
        })
    },
    // 点击确定按钮修改目录
    EditDirectory() {
      this.$refs.directoryRef.validate(valid => {
        if (!valid) return false
        update(this.editObject)
          .then(data => {
            this.$message.success('修改学科目录成功')
            this.$emit('EditDirectory')
          })
          .catch(e => {
            this.$message('修改失败，请稍后重试')
            this.$emit('close')
          })
      })
    }
  }
}
</script>
<style lang="scss" scoped></style>
