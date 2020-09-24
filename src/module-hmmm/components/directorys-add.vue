<template>
  <div class="add-form">
    <el-dialog title="新增目录" :visible.sync="addDialogVisible" width="30%" @close="handleClose">
      <el-form
        :model="directory"
        :rules="rules"
        ref="directoryRef"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item :label="$t('table.subjectName')">
          <el-select v-model="directory.subjectID" placeholder="请选择" clearable>
            <el-option
              v-for="item in list"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item :label="$t('table.directoryName')" prop="directoryName">
          <el-input v-model="directory.directoryName" placeholder="请输入目录名称"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer">
        <el-button @click="$emit('close',false)">{{$t('table.cancel')}}</el-button>
        <el-button type="primary" @click="addDirectory">{{$t('table.confirm')}}</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects'
import { add } from '@/api/hmmm/directorys'

export default {
  name: 'DirectoryAdd',
  data() {
    return {
      // 控制添加弹层的显示与隐藏
      addDialogVisible: true,
      directory: {
        subjectID: null,
        directoryName: ''
      },
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
        .catch((e) => {
          this.$message('错了哦，这是一条错误消息')
        })
    },
    // 监听dialog对话框关闭事件
    handleClose() {
      this.directory.subjectID = null
      this.$refs.directoryRef.resetFields()
      this.$emit('close', false)
    },
    // 点击确定按钮添加目录
    addDirectory(val) {
      this.$refs.directoryRef.validate((valid) => {
        if (valid) {
          add({
            subjectID: this.directory.subjectID,
            directoryName: this.directory.directoryName
          })
            .then((data) => {
              this.$message('添加学科目录成功')
            })
            .catch((e) => {
              this.$message('错了哦，这是一条错误消息')
            })
        } else {
          return false
        }
        this.$emit('addDirectory')
      })
    }
  }
}
</script>
<style lang="scss" scoped>
</style>
