<template>
  <div class="add-form">
    <el-dialog title="修改目录" :visible.sync="EditDialogVisible" width="30%" @close="handleClose">
      <el-form :model="editObject" :rules="rules" ref="directoryRef" label-width="100px">
        <el-form-item :label="$t('table.subjectName')">
          <el-select v-model="editObject.subjectID" placeholder="请选择" clearable>
            <el-option v-for="item in list" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item :label="$t('table.directoryName')" prop="directoryName">
          <el-input v-model.trim="editObject.directoryName" placeholder="请输入目录名称"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer">
        <el-button @click="$emit('close', false)">{{ $t('table.cancel') }}</el-button>
        <el-button type="primary" @click="EditDirectory">{{ $t('table.confirm') }}</el-button>
      </span>
    </el-dialog>
  </div>
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
      editObject: JSON.parse(JSON.stringify(this.directoryObj)),
      // 控制添加弹层的显示与隐藏
      EditDialogVisible: true,
      // 学科列表信息
      list: [],
      // 新增目录验证规则
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
          this.$message('错了哦，这是一条错误消息')
        })
    },
    // 监听dialog对话框关闭事件
    handleClose() {
      // this.directory.subjectID = null
      // this.$refs.directoryRef.resetFields()
      this.$emit('close', false)
    },
    // 点击确定按钮修改目录
    EditDirectory() {
      this.$refs.directoryRef.validate(valid => {
        if (valid) {
          update(this.editObject)
            .then(data => {
              this.$message.success('修改学科目录成功')
              this.$emit('EditDirectory')
            })
            .catch(e => {
              this.$message('这是一条错误消息')
              this.$emit('close')
            })
        } else {
          return false
        }
      })
    }
  }
}
</script>
<style lang="scss" scoped></style>
