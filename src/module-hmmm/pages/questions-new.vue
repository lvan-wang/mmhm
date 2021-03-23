<template>
  <div class='container'>
    <!-- 卡片 -->
    <el-card>
      <!-- 头部 -->
      <div class="card_header">{{id === '0' ? '试题录入' : '试题修改'}}</div>
      <!-- 内容 500 -->
      <el-form ref="form" :model="form" label-width="120px" :rules="formRules" class="card_body">
        <!-- 学科 -->
        <el-form-item label="学科：" prop="subjectID">
          <el-select  @change="selectCatalogue" v-model="form.subjectID" placeholder="请选择" class="select_content">
            <el-option v-for="item, index in subjectList" :key="index" :value="item.value" :label="item.label"></el-option>
          </el-select>
        </el-form-item>
        
        <!-- 目录 -->
        <el-form-item label="目录：" prop="catalogID">
          <el-select v-model="form.catalogID" placeholder="请选择" class="select_content">
            <el-option v-for="item, index in twoLevelDirectory" :key="index" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>

        <!-- 企业 -->
        <el-form-item label="企业：" prop="region">
          <el-select v-model="form.region" placeholder="请选择活动区域" class="select_content">
            <el-option label="区域一" value="shanghai"></el-option>
            <el-option label="区域二" value="beijing"></el-option>
          </el-select>
        </el-form-item>

        <!-- 城市 -->
        <el-form-item label="城市：" prop="region">
          <el-select v-model="form.region" placeholder="请选择活动区域" class="select_content">
            <el-option label="区域一" value="shanghai"></el-option>
            <el-option label="区域二" value="beijing"></el-option>
          </el-select>
        </el-form-item>
        <!-- 方向 -->
        <el-form-item label="方向：" prop="direction">
          <el-select v-model="form.direction" placeholder="请选择" class="select_content">
            <el-option v-for="item, index in directionList" :key="index" :label="item.label" :value="item.label"></el-option>
          </el-select>
        </el-form-item>
        <!-- 题型 -->
        <el-form-item label="题型：" prop="questionType">
          <el-radio-group v-model="form.questionType">
            <el-radio :label="1">单选题</el-radio>
            <el-radio :label="2">多选题</el-radio>
            <el-radio :label="3">简答题</el-radio>
          </el-radio-group>
        </el-form-item>
        <!-- 难度 -->

        <el-form-item label="难度：" prop="difficulty">
          <el-radio-group v-model="form.difficulty">
            <el-radio :label="3">简单</el-radio>
            <el-radio :label="6">一般</el-radio>
            <el-radio :label="9">困难</el-radio>
          </el-radio-group>
        </el-form-item>

        <!-- 题干 富文本 -->
        <el-form-item label="题干：" prop="question">
          <div class="rich_text">
            <quill-editor style="height:100%" v-model="form.question"></quill-editor>
          </div>
        </el-form-item>
        
        <input type="file" style="display:none" ref="inputFile">
        <!-- 选项 -->
        <el-form-item label="选项：" prop="region">
          <div>
            <el-radio-group v-model="radio">
              <div class="block">
                <el-radio :label="3">A:
              </el-radio>
              <el-input v-model="form.name"></el-input>
              <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i class="el-icon-circle-close icon_close"></i></el-button>
              </div>

              <div class="block">
                <el-radio :label="6">B:
              </el-radio>
              <el-input v-model="form.name"></el-input>
              <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i class="el-icon-circle-close icon_close"></i></el-button>
              
              </div>

              <div class="block">
                <el-radio :label="9">C:
              </el-radio>
              <el-input v-model="form.name"></el-input>
              <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i class="el-icon-circle-close icon_close"></i></el-button>
              </div>

              <div class="block">
                <el-radio :label="10">D:
              </el-radio>
              <el-input v-model="form.name"></el-input>
              <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i class="el-icon-circle-close icon_close"></i></el-button>
              </div>

              <div class="block">
                <el-button type="danger" size="small" disabled>+添加选项与答案</el-button>
              </div>
             
            </el-radio-group>
          </div>
        </el-form-item>

        <!-- 解析视频 -->
        <el-form-item label="解析视频：" prop="region">
          <el-input v-model="form.name"  class="select_content"></el-input>
        </el-form-item>

        <!-- 答案解析 富文本-->
        <el-form-item label="答案解析：" prop="region">
          <div class="rich_text">
            <quill-editor style="height:100%" v-model="form.remarks"></quill-editor>
          </div> 
        </el-form-item>

        <!-- 题目备注 -->
        <el-form-item label="题目备注：">
          <el-input type="textarea"  class="select_content" v-model="form.remarks"></el-input>
        </el-form-item>

        <!-- 试题标签 -->
        <el-form-item label="试题标签：" >
          <el-select v-model="form.tags" placeholder="请选择" class="select_content">
                <el-option v-for="item, index in tagName" :key="index" :label="item" :value="item"></el-option>
              </el-select>
        </el-form-item>

      </el-form>
    </el-card>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects'
import { detail } from '@/api/hmmm/questions'
export default {
  data () {
    return {
      subjectList: [], // 学科数据
      twoLevelDirectory: [], // 学科的二级目录
      tagName: [], // 学科的标签
      form: {
        subjectID: '', // 学科的相关数据
        catalogID: '', // 二级目录的数据
        direction: '', // 方向
        questionType: '', // 试题类型
        difficulty: '', // 难度
        question: '', // 题干---富文本
        enterpriseID: '', // 企业
        tags: [], // 学科的标签
        remarks: '', // 题目备注
        name: ''
      },
      radio: 3,
      // 验证规则
      formRules: {
        region: [
          { required: true, message: '请选择', trigger: 'change' }
        ],
        // 学科的验证规则
        subjectID: [
          { required: true, message: '请选择学科', trigger: 'change' }
        ],
        // 方向的验证规则
        direction: [
          { required: true, message: '请选择方向', trigger: 'change' }
        ],
        // 题型的验证规则
        questionType: [
          { required: true, message: '请选择题型', trigger: 'change' }
        ],
        // 难度的验证规则
        difficulty: [
          { required: true, message: '请选择难度', trigger: 'change' }
        ]
      },
      // 方向
      directionList: [
        { value: 'o2o', label: 'o2o'},
        { value: '外包服务', label: '外包服务'},
        { value: '企业服务', label: '企业服务'},
        { value: '互联网金融', label: '互联网金融'},
        { value: '企业咨询', label: '企业咨询'},
        { value: '互联网', label: '互联网'},
        { value: '电子商务', label: '电子商务'},
        { value: '其他', label: '其他'}
      ]
    }
  },
  props: {
    id: {
      type: [ String, Number, Object],
      required : true
    }
  },
  created () {
    // 获取学科列表
    this.getsubject()
    // 根据id获取当前这一条数据的所有内容
    this.getIdList()
  },
  methods: {
    // 获取学科列表
    async getsubject () {
      try {
        const { data } = await simple()
        // console.log(data)
        this.subjectList = data
      } catch (err) {
        this.$message.error('获取数据失败')
      }
    },
    // 学科的change事件，获取二级目录
    async selectCatalogue () {
      this.tagName = []
      try {
        // 获取学科二级目录的请求
        console.log(this.form.subjectID)
        const id = {id: this.form.subjectID}
        const { data } = await detail(id)
        // console.log(data)
        this.twoLevelDirectory = data.twoLevelDirectory

        // 获取当前学科标签的请求
        const res = await createAPI('/tags/' + this.form.subjectID, 'get')
        console.log(res)
        this.tagName.push(res.data.tagName)

      } catch (err) {
      }
    },
    // 根据id获取当前这一条数据的所有内容
    async getIdList () {
      try {
        const { data } = await detail({id: this.id})
        console.log(data)
        this.remarks = data.remarks
      } catch (err) {
        this.$message.error('获取数据失败')
      }
    }
  }
}
</script>

<style scoped rel="stylesheet/scss" lang="scss">
.card_header {
    padding-bottom:20px;
    border-bottom: 1px solid #ebeef5;
    box-sizing: border-box;
}

.card_body {
  padding: 20px;
  .select_content {
    width: 400px;
  }
}

.rich_text {
  height: 250px;
  padding-bottom: 100px;
}

.block {
  display: flex;
  justify-items: center;
  align-items: center;
  padding: 5px 0;
}
.onpicBtn {
  position: relative;
  border: 1px dashed #999;
  height: 50px;
  .icon_close {
    position: absolute;
    right: -8px;
    top: -8px;
    font-size: 20px;
  }
}

</style>
