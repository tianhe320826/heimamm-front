<template>
  <div class="add-form">
    <el-dialog :title="text + pageTitle" :visible.sync="dialogFormVisible" width="60%">
      <el-form :rules="ruleInline" ref="dataForm" :model="formBase" label-position="left" label-width="80px" style="width: 90%">
        <el-form-item :label="$t('table.articleTitle')" prop="title">
          <el-input v-model="formBase.title" placeholder="请输入文章标题"></el-input>
        </el-form-item>
        <el-form-item :label="$t('table.articleBody')" prop="articleBody" class="articleBody">
          <quill-editor v-model="formBase.articleBody" :options="editorOption"></quill-editor>
        </el-form-item>
        <el-form-item :label="$t('table.videoURL')">
          <el-input v-model="formBase.videoURL"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="handleClose">{{ $t('table.cancel') }}</el-button>
        <el-button type="primary" @click="createData">{{ $t('table.confirm') }}</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { update, add } from '@/api/hmmm/articles'
export default {
  name: 'articlesAdd',
  props: ['text', 'pageTitle', 'ruleInline', 'formBase'],
  data() {
    return {
      dialogFormVisible: false,
      editorOption: {
        modules: {
          toolbar: [
            ['bold', 'italic', 'underline', 'strike'], // 加粗 斜体 下划线 删除线
            ['blockquote', 'code-block'], // 引用  代码块
            // [{ header: 1 }, { header: 2 }], // 1、2 级标题
            [{ list: 'ordered' }, { list: 'bullet' }], // 有序、无序列表
            // [{ script: 'sub' }, { script: 'super' }], // 上标/下标
            // [{ indent: '-1' }, { indent: '+1' }], // 缩进
            // [{'direction': 'rtl'}],                         // 文本方向
            // [{ size: ['small', false, 'large', 'huge'] }], // 字体大小
            // [{ header: [1, 2, 3, 4, 5, 6, false] }], // 标题
            // [{ color: [] }, { background: [] }], // 字体颜色、字体背景颜色
            // [{ font: [] }], // 字体种类
            // [{ align: [] }], // 对齐方式
            // ['clean'], // 清除文本格式
            ['link', 'image'] // 链接、图片
          ] // 工具菜单栏配置
        }
      }
    }
  },
  components: {},
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
    // 清空表单数据
    resetContent() {
      this.$refs.dataForm.resetFields()
      this.formBase.videoURL = ''
      // console.log(this.formBase)
    },
    // 退出
    handleClose() {
      this.$emit('handleCloseModal')
      this.resetContent()
    },
    // 表单提交
    createData() {
      this.$refs.dataForm.validate((valid) => {
        if (valid) {
          this.$emit('handleCloseModal')
          const data = {
            ...this.formBase
          }
          if (this.formBase.id) {
            update(data).then(() => {
              this.$emit('newDataes', this.formBase)
              this.resetContent()
            })
          } else {
            add(this.formBase).then(() => {
              this.$emit('newDataes', this.formBase)
              this.resetContent()
            })
          }
        } else {
          this.$Message.error('*号为必填项!')
        }
      })
    }
  },
  // 挂载结束

  mounted: function () {},
  // 创建完毕状态
  created() {},
  // 组件更新
  updated: function () {}
}
</script>
<style>
.el-form--label-left .el-form-item__label {
  text-align: right;
}
.el-form-item--medium {
  margin-bottom: 22px;
}
.el-dialog__footer {
  text-align: center;
}
.quill-editor {
  height: 250px;
  margin-bottom: 50px;
}
</style>
