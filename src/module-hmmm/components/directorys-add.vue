<template>
  <el-dialog title="新增目录" :visible.sync="addDialogVisible" width="30%" @close="$emit('close')">
    <el-form :model="directory" :rules="rules" ref="directoryRef" label-width="80px">
      <el-form-item :label="$t('table.subjectName')" v-if="!subjectAdd.subjectID">
        <el-select v-model="directory.subjectID" placeholder="请选择" clearable>
          <el-option v-for="item in list" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item :label="$t('table.directoryName')" prop="directoryName">
        <el-input v-model.trim="directory.directoryName" placeholder="请输入目录名称" @keyup.enter.native="addDirectory" clearable></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer">
      <el-button @click="$emit('close')">{{ $t('table.cancel') }}</el-button>
      <el-button type="primary" @click="addDirectory">{{ $t('table.confirm') }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { simple } from '@/api/hmmm/subjects'
import { add } from '@/api/hmmm/directorys'

export default {
  name: 'DirectoryAddIndex',
  props: {
    subjectAdd: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      // 控制添加弹层的显示与隐藏
      addDialogVisible: true,
      // 添加目录信息对象
      directory: {
        subjectID: null,
        directoryName: ''
      },
      // 存储学科列表
      list: [],
      // 新增目录验证规则
      rules: {
        directoryName: [{ required: true, message: '请输入目录名称', trigger: 'blur' }]
      }
    }
  },
  created() {
    if (this.subjectAdd.subjectID === undefined) {
      this.getSubjectList()
    }
  },
  methods: {
    // 获取学科列表
    getSubjectList() {
      simple()
        .then(({ data }) => {
          this.list = data
        })
        .catch((e) => {
          this.$message('获取学科列表失败')
        })
    },
    // 点击确定按钮添加目录
    addDirectory() {
      this.$refs.directoryRef.validate((valid) => {
        if (!valid) return false
        add({
          subjectID: this.directory.subjectID ? this.directory.subjectID : this.subjectAdd.subjectID - 0,
          directoryName: this.directory.directoryName
        })
          .then((data) => {
            this.$emit('addDirectory')
            this.$message.success('添加学科目录成功')
          })
          .catch((e) => {
            this.$emit('close')
            this.$message('添加失败，请稍后重试')
          })
      })
    }
  }
}
</script>
<style lang="scss" scoped></style>
