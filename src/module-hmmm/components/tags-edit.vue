<template>
  <el-dialog title="修改标签" :visible.sync="EditDialogVisible" width="30%" @close="$emit('close')">
    <el-form :model="editObject" :rules="rules" ref="tagRef" label-width="100px">
      <el-form-item :label="$t('table.subjectName')" v-if="!subjectEdit.subjecttag">
        <el-select v-model="editObject.subjectID" placeholder="请选择" clearable>
          <el-option v-for="item in list" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item :label="$t('table.tagName')" prop="tagName">
        <el-input v-model.trim="editObject.tagName" placeholder="请输入标签名称" @keyup.enter.native="EditTag"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer">
      <el-button @click="$emit('close')">{{ $t('table.cancel') }}</el-button>
      <el-button type="primary" @click="EditTag">{{ $t('table.confirm') }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
import { update } from '@/api/hmmm/tags'
import { simple } from '@/api/hmmm/subjects'

export default {
  name: 'TagEditIndex',
  props: {
    tagObj: {
      type: Object,
      required: true
    },
    subjectEdit: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      editObject: JSON.parse(JSON.stringify(this.tagObj)),
      // 控制添加弹层的显示与隐藏
      EditDialogVisible: true,
      // 学科列表信息
      list: [],
      // 新增目录验证规则
      rules: {
        tagName: [{ required: true, message: '请输入标签名称', trigger: 'blur' }]
      }
    }
  },
  created() {
    if (this.subjectEdit.subjectID === undefined) {
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
    // 点击确定按钮修改标签
    EditTag() {
      this.$refs.tagRef.validate((valid) => {
        if (!valid) return false
        update({
          id: this.editObject.id,
          subjectID: this.editObject.subjectID ? this.editObject.subjectID : this.subjectEdit.subjectID,
          tagName: this.editObject.tagName
        })
          .then((data) => {
            this.$message.success('修改学科目录成功')
            this.$emit('EditTag')
          })
          .catch((e) => {
            this.$message('修改失败，请稍后重试')
            this.$emit('close')
          })
      })
    }
  }
}
</script>
<style lang="scss" scoped></style>
