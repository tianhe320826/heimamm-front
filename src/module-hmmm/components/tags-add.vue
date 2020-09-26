<template>
  <el-dialog title="新增标签" :visible.sync="addDialogVisible" width="30%" @close="$emit('close', false)">
    <el-form :model="tags" :rules="rules" ref="tagsRef" label-width="80px">
      <el-form-item :label="$t('table.subjectName')" v-if="!subjecttag">
        <el-select v-model="tags.subjectID" placeholder="请选择" clearable>
          <el-option v-for="item in list" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item :label="$t('table.tagName')" prop="tagName">
        <el-input v-model.trim="tags.tagName" placeholder="请输入目录名称" @keyup.enter.native="addTag"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer">
      <el-button @click="$emit('close', false)">{{ $t('table.cancel') }}</el-button>
      <el-button type="primary" @click="addTag">{{ $t('table.confirm') }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { simple } from '@/api/hmmm/subjects'
import { add } from '@/api/hmmm/tags'

export default {
  name: 'TagsAddIndex',
  props: ['subjecttag', 'subjectID'],
  data() {
    return {
      // 控制添加弹层的显示与隐藏
      addDialogVisible: true,
      tags: {
        subjectID: null,
        tagName: ''
      },
      // 简单学科列表信息
      list: [],
      // 新增标签验证规则
      rules: {
        tagName: [{ required: true, message: '请输入标签名称', trigger: 'blur' }]
      }
    }
  },
  created() {
    this.getSubjectList()
  },
  methods: {
    // 获取简单学科列表
    getSubjectList() {
      simple()
        .then(({ data }) => {
          this.list = data
        })
        .catch(e => {
          this.$message('获取学科列表失败')
        })
    },
    // 点击确定按钮添加目录
    addTag() {
      this.$refs.tagsRef.validate(valid => {
        if (!valid) return false
        add({
          subjectID: this.tags.subjectID ? this.tags.subjectID : this.subjectID,
          tagName: this.tags.tagName
        })
          .then(data => {
            this.$message.success('添加学科标签成功')
            this.$emit('addTags')
          })
          .catch(e => {
            this.$message('添加失败，请稍后重试')
            this.$emit('close')
          })
      })
    }
  }
}
</script>

<style lang="scss" scoped></style>
