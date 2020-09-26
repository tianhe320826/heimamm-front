<template>
  <div class="questions-new-container">
    <el-card>
      <el-form :rules="rules">
        <div slot="header" class="clearfix">
          <span>试题录入 </span>
        </div>
        <!-- 选择框 -->
        <el-form-item label="学科：" label-width="10%" prop="subject">
          <el-select v-model="reqParmas.subjectID" @change="handelSubject" clearable placeholder="请选择" style="width: 30%">
            <el-option v-for="item in subjectOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录：" label-width="10%" prop="directory">
          <el-select v-model="reqParmas.catalogID" @change="handelDir" clearable placeholder="请选择" style="width: 30%">
            <el-option v-for="item in dirOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="企业：" label-width="10%" prop="companys">
          <el-select v-model="reqParmas.enterpriseID" @change="handleEnterpriseID" clearable placeholder="请选择" style="width: 30%">
            <el-option v-for="item in companyOptions" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="省：" label-width="10%" prop="province">
          <el-select class="filter-item" style="width: 120px" v-model="reqParmas.province" @keyup.enter="handleFilter" @change="handleProvince" filterable>
            <el-option v-for="item in citySelect.province" :key="item" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <!--  -->
        <el-form-item label="市：" label-width="10%" prop="city">
          <el-select class="filter-item" style="width: 120px" v-model="reqParmas.city" @keyup.enter="handleFilter" @change="handleCity" filterable>
            <el-option v-for="item in citySelect.cityDate" :key="item" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="方向：" label-width="10%" prop="direction">
          <el-select v-model="reqParmas.direction" clearable placeholder="请选择" @change="reqParmas.direction = $event" style="width: 30%">
            <el-option v-for="(item, index) in direction" :key="index" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <!-- 选择按钮 -->
        <el-form-item label="题型：" label-width="10%" prop="questionType">
          <el-radio-group v-model="reqParmas.questionType" @change="handelSingle">
            <el-radio v-for="(item, index) in questionType" :key="index" :label="item.label" border></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="难度：" label-width="10%" prop="difficulity">
          <el-radio-group v-model="reqParmas.difficulty">
            <el-radio v-for="(item, index) in difficulty" :key="index" :label="item.label" @change="reqParmas.difficulty = $event" border></el-radio>
          </el-radio-group>
        </el-form-item>
        <!-- 富文本编辑器 -->
        <el-form-item label="题干：" label-width="10%" prop="question">
          <quill-editor v-model="reqParmas.question" style="width: 90%"></quill-editor>
        </el-form-item>
        <!-- 选项 -->
        <el-form-item v-show="isOpeionsShow" label="选项：" label-width="10%">
          <div class="option_item" v-for="(item, index) in questionsOptinos" :key="index">
            <el-radio v-show="isRadioShow" v-model="isRadioed" @change="handelradioChange(index)" :label="String.fromCharCode(item)" border></el-radio>
            <el-checkbox v-show="isCheckBoxShow" v-model="isCheckList" @change="handelcheckChange(index)" :label="String.fromCharCode(item)" border></el-checkbox>
            <el-input style="width: 30%" placeholder="请输入内容" :value="reqParmas.options[index].title" @input="handleTitle(index, $event)" clearable> </el-input>
            <el-upload action="https://jsonplaceholder.typicode.com/posts/" list-type="picture-card" :on-preview="handlePictureCardPreview" :on-remove="handleRemove">
              <span class="uploadimg">上传图片</span>
            </el-upload>
            <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="reqParmas.img" alt="" />
            </el-dialog>
          </div>
        </el-form-item>
        <el-form-item label-width="10%">
          <el-button type="danger" v-show="isBtnShow" :disabled="isBtnDisable" @click="addOptions">+增加选项与答案</el-button>
        </el-form-item>
        <!-- <question-select>
          <template slot="options"> </template>
        </question-select> -->
        <!-- 视频解析地址 -->
        <el-form-item label="解析视频地址" label-width="10%">
          <el-input placeholder="请输入解析视频地址" v-model="reqParmas.videoURL" clearable style="width: 30%"> </el-input>
        </el-form-item>
        <!-- 答案解析 -->
        <el-form-item label="答案解析：" label-width="10%" prop="answer">
          <quill-editor v-model="reqParmas.answer"></quill-editor>
        </el-form-item>
        <!-- 题目备注 -->
        <el-form-item label="题目备注：" label-width="10%">
          <el-input style="width: 30%" type="textarea" v-model="reqParmas.remarks"></el-input>
        </el-form-item>
        <!-- 试题标签 -->
        <el-form-item label="试题标签" label-width="10%">
          <el-select style="width: 30%" v-model="reqParmas.tags" multiple filterable allow-create default-first-option @change="reqParmas.tags = $event" placeholder="请选择">
            <el-option v-for="(item, index) in tagsOptinos" :key="index" :label="item.label" :value="item.label"> </el-option>
          </el-select>
        </el-form-item>
        <!-- 确认提交 -->
        <el-form-item label-width="10%">
          <el-button type="primary" @click="Submit">确认提交</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
import { simple as subSimple } from '@/api/hmmm/subjects'
import { simple as dirSimple } from '@/api/hmmm/directorys'
import { simple as simpleTags } from '@/api/hmmm/tags'
import { provinces, citys } from '@/api/hmmm/citys.js'
import { difficulty, questionType, direction } from '@/api/hmmm/constants'
import { list as companysList } from '@/api/hmmm/companys'
import { add as addQuestion } from '@/api/hmmm/questions'
// 导入选择框静态数据
export default {
  name: 'QuestionsNew',
  components: {},
  props: {},
  data() {
    return {
      subjectOptions: [],
      dirOptions: [],
      companyOptions: [],
      cityOptions: [],
      dialogVisible: false,
      questionsOptinos: [65, 66, 67, 68],
      tagsOptinos: [],
      citySelect: {
        province: [],
        cityDate: []
      },
      isOpeionsShow: true,
      isBtnDisable: true,
      isRadioed: false,
      isCheckList: [],
      isBtnShow: true,
      isRadioShow: true,
      isCheckBoxShow: false,
      // 请求参数
      reqParmas: {
        // number: 0, // 试题编号 后台生成
        subjectID: '', // 学科
        catalogID: '', // 目录
        enterpriseID: '', // 企业
        province: '', // 城市
        city: '', // 地区
        direction: '', // 方向
        questionType: '', // 题型
        difficulty: '', // 难度
        question: '', // 题干
        options: [
          {
            // 选项 数组里有对象
            code: '', // 代码
            title: '', // 标题
            img: '', // 图片URL
            isRight: false // 是否为正确答案
          },
          {
            // 选项 数组里有对象
            code: '', // 代码
            title: '', // 标题
            img: '', // 图片URL
            isRight: false // 是否为正确答案
          },
          {
            // 选项 数组里有对象
            code: '', // 代码
            title: '', // 标题
            img: '', // 图片URL
            isRight: false // 是否为正确答案
          },
          {
            // 选项 数组里有对象
            code: '', // 代码
            title: '', // 标题
            img: '', // 图片URL
            isRight: false // 是否为正确答案
          }
        ],
        videoURL: '',
        answer: '',
        remarks: '',
        tags: '',
        isPrefect: false
      },
      rules: {
        subject: [{ required: true, message: '不能为空', trigger: 'blur' }],
        directory: [{ required: true, message: '不能为空', trigger: 'blur' }],
        companys: [{ required: true, message: '不能为空', trigger: 'blur' }],
        province: [{ required: true, message: '不能为空', trigger: 'blur' }],
        city: [{ required: true, message: '不能为空', trigger: 'blur' }],
        direction: [{ required: true, message: '不能为空', trigger: 'blur' }],
        questionType: [{ required: true, message: '不能为空', trigger: 'blur' }],
        difficulity: [{ required: true, message: '不能为空', trigger: 'blur' }],
        question: [{ required: true, message: '不能为空', trigger: 'blur' }],
        answer: [{ required: true, message: '不能为空', trigger: 'blur' }]
      }
    }
  },
  computed: {
    difficulty() {
      return difficulty
    },
    questionType() {
      return questionType
    },
    direction() {
      return direction
    }
  },
  watch: {},
  created() {
    this.Loading()
  },
  mounted() {},
  methods: {
    async Loading() {
      this.getSubData()
      this.getCityData()
      this.getCompanys()
    },
    async getSubData() {
      const { data: subRes } = await subSimple()
      this.subjectOptions = subRes

      // console.log(subRes) // 数组里有对象 对象有value
    },
    handelSubject(e) {
      // e是当前学科的id item.value
      this.reqParmas.subjectID = e
      // 获取学科对应的目录
      this.getDirData(e)
    },
    async getDirData(e) {
      const { data: resDir } = await dirSimple({ subjectID: e })
      // TODO应展示当前学科的二级目录
      // console.log(e, resDir)
      this.dirOptions = resDir
    },
    handelDir(e) {
      this.reqParmas.catalogID = e
      // 获取目录对应的标签 传递学科id
      this.getTagsData(e)
    },
    async getTagsData(e) {
      const { data: resTags } = await simpleTags(e)
      this.tagsOptinos = resTags
      // console.log(resTags)
    },
    async getCompanys() {
      const { data: companys } = await companysList()
      // console.log(companys)
      companys.items.forEach((item, index) => {
        // console.log(item.shortName)
        this.companyOptions.push({ label: item.shortName, value: item.id })
      })
      // console.log(this.companyOptions)
    },
    // 获取省
    getCityData() {
      this.citySelect.province = provinces()
    },
    // 选省获取到市
    handleProvince(e) {
      this.citySelect.cityDate = citys(e)
      // console.log(this.reqParmas)
    },
    handleCity(e) {
      this.reqParmas.city = e
    },
    handelSingle(e) {
      this.reqParmas.questionType = e
      // console.log(e)
      // 单选 显示四个选项 按钮禁用
      // 多选
      // 简答  隐藏这个模块
      if (e === '简答') {
        this.isOpeionsShow = false
        this.isBtnShow = false
      } else if (e === '多选') {
        // 框变成方的 按钮
        this.isBtnShow = true
        this.isOpeionsShow = true
        this.isBtnDisable = false
        this.isCheckBoxShow = true
        this.isRadioShow = false
      } else {
        this.isBtnShow = true
        this.isOpeionsShow = true
        this.isBtnDisable = true
      }
    },
    handleTitle(index, e) {
      this.reqParmas.options[index].title = e
      // console.log(e)
    },
    // 图片
    handleRemove(file, fileList) {
      // console.log(file, fileList)
    },
    handlePictureCardPreview(file) {
      this.reqParmas.img = file.url
      this.dialogVisible = true
    },
    handleEnterpriseID(e) {
      // console.log(e)
      this.reqParmas.enterpriseID = e
    },

    addOptions() {
      const len = this.questionsOptinos.length
      const e = this.questionsOptinos[len - 1]
      this.questionsOptinos.push(e + 1)
      // 新增一个data中绑定的数据
      this.reqParmas.options.push({
        code: '',
        title: '',
        img: '',
        isRight: false
      })
    },
    handelradioChange(index) {
      // 如果当前被选中
      this.reqParmas.options[index].isRight = true
      // code是啥
      this.reqParmas.options[index].code = ''
    },
    handlecheckChange(index) {
      // 如果当前被选中
      this.reqParmas.options[index].isRight = true
      // code是啥
      this.reqParmas.options[index].code = ''
    },
    Submit() {
      // console.log(this.reqParmas)
      this.$confirm('即将提交，请检查题目信息 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await addQuestion(this.reqParmas)
            .then(response => {
              this.$message.success('已成功' + status + '!')
              this.getList(this.reqParmas)
            })
            .catch(response => {
              this.$message.error(status + '失败!')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    }
  }
}
</script>

<style lang="less" scoped>
.questions-new-container {
  .option_item {
    display: flex;
    flex-direction: row;
    align-items: center;
  }
  /deep/ .ql-editor {
    min-height: 200px;
  }
  /deep/ .el-upload {
    width: 100px;
    height: 80px;
    margin-left: 15px;
    /deep/ .uploadimg {
      display: block;
      height: 80px;
      line-height: 80px;
    }
  }
}
</style>
