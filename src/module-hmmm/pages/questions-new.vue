<template>
  <div class='container'>
    <!-- 卡片 -->
    <el-card>
      <!-- 头部 -->
      <div class="card_header">{{id === '0' ? '试题录入' : '试题修改'}}</div>
      <!-- 内容 500 -->
      <el-form ref="addFormRef" :model="form" label-width="120px" :rules="formRules" class="card_body">
        <!-- 学科 -->
        <el-form-item label="学科：" prop="subjectID">
          <el-select  @change="selectCatalogue" v-model="form.subjectID" placeholder="请选择学科" class="select_content">
            <el-option v-for="item, index in subjectList" :key="index" :value="item.value" :label="item.label"></el-option>
          </el-select>
        </el-form-item>
        
        <!-- 目录 -->
        <el-form-item label="目录：">
          <el-select v-model="form.catalogID" placeholder="请选择目录" class="select_content">
            <el-option v-for="item, index in twoLevelDirectory" :key="index" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>

        <!-- 企业 -->
        <el-form-item label="企业：" prop="enterpriseID">
          <el-select v-model="form.enterpriseID" placeholder="请选择企业" class="select_content">
            <el-option v-for="item, index in companysList" :key="index" :label="item.company" :value="item.creatorID"></el-option>
          </el-select>
        </el-form-item>

        <!-- 城市 -->
        <el-form-item label="城市：" prop="province" >
          <el-select @change="selectProvinces" v-model="form.province" placeholder="请选择" size="small">
            <el-option
            v-for="item, index in provinces"
            :key="index"
            :label="item"
            :value="item">
          </el-option>
          </el-select>
          <el-select v-model="form.city" placeholder="请选择" size="small">
          <el-option
            v-for="item, index in citys"
            :key="index"
            :label="item"
            :value="item">
          </el-option>
        </el-select>
        </el-form-item>
        <!-- 方向 -->
        <el-form-item label="方向：" prop="direction">
          <el-select v-model="form.direction" placeholder="请选择方向" class="select_content">
            <el-option v-for="item, index in directionList" :key="index" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <!-- 题型 -->
        <el-form-item label="题型：" prop="questionType">
          <el-radio-group v-model="form.questionType">
            <el-radio :label="'1'">单选题</el-radio>
            <el-radio :label="'2'">多选题</el-radio>
            <el-radio :label="'3'">简答题</el-radio>
          </el-radio-group>
        </el-form-item>
        <!-- 难度 -->

        <el-form-item label="难度：" prop="difficulty">
          <el-radio-group v-model="form.difficulty">
            <el-radio :label="'1'">简单</el-radio>
            <el-radio :label="'2'">一般</el-radio>
            <el-radio :label="'3'">困难</el-radio>
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
        <el-form-item label="选项：" class="optionWidth" v-show="form.questionType !== '3'">
          <!-- 添加的情况 -->
          <div v-if="id === '0' || id === ':id'">
            <!-- <el-checkbox label="复选框 A"></el-checkbox> -->
            <!-- 单选题 -->
            <div v-if="form.questionType === '1'">
             <el-radio-group v-model="options">

              <div class="block" v-for="item, index in optionCheckbox" :key="index" >
                <el-radio :label="item.id">{{item.code}}</el-radio>
                <el-input v-model="item.title"></el-input>
                <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i class="el-icon-circle-close icon_close"></i></el-button>
              </div>

              <!-- 添加选项的按钮 -->
              <div class="block">
                  <el-button type="danger" size="small" :disabled="form.questionType === '1' ? true : false">+添加选项与答案</el-button>
              </div>
             
             </el-radio-group>
            </div>
            <!-- 多选题 -->
            <div v-else>
              <div v-for="item, index in optionCheckbox" :key="index" class="questionType_checkbox">
              <el-checkbox-group v-model="form.options">
                <div class="block">
                    <el-checkbox :label="item.code" style="width:80px"
                    @click="item.optionCheckbox.isRight = !item.optionCheckbox.isRight"
                    ></el-checkbox>
                <el-input v-model="item.title"></el-input>
                <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i class="el-icon-circle-close icon_close"></i></el-button>
                </div>
              </el-checkbox-group>
              </div>

              <!-- 添加选项的按钮 -->
              <div class="block">
                  <el-button type="danger" size="small" :disabled="form.questionType === '1' ? true : false" @click="addOptions">+添加选项与答案</el-button>
              </div>
            </div>
          </div>
         <!-- 修改的情况 -->
         <div v-else>
           <!-- 单选题的情况 -->
          <div v-if="form.questionType === '12'">
             <div v-for="item, index in options" :key="index" class="questionType_checkbox">
              <el-radio-group v-model="checkboxItem">
                <div class="block">
                    <el-radio :label="item.code" style="width:80px"></el-radio>
                <el-input v-model="item.title"></el-input>
                <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i   class="el-icon-circle-close icon_close"></i></el-button>
                </div>
              </el-radio-group>
            </div>
          </div>
          <!-- 多选题的情况 -->
          <div v-else-if="form.questionType === '2'">
            <div v-for="item, index in options" :key="index" class="questionType_checkbox">
              <el-checkbox-group v-model="checkboxItem">
                <div class="block">
                    <el-checkbox :label="item.code" style="width:80px"></el-checkbox>
                  <!-- <el-radio :label="3">A: -->
                <!-- </el-radio> -->
                <el-input v-model="item.title"></el-input>
                <el-button class="onpicBtn" @click="$refs.inputFile.click()">上传图片<i   class="el-icon-circle-close icon_close"></i></el-button>
                </div>
              </el-checkbox-group>
            </div>
          </div>
          <!-- 如果没有选项 -->
        </div>

        </el-form-item>

        <!-- 解析视频 -->
        <el-form-item label="解析视频：">
          <el-input v-model="form.videoURL" class="select_content"></el-input>
        </el-form-item>

        <!-- 答案解析 富文本-->
        <el-form-item label="答案解析：" prop="answer">
          <div class="rich_text">
            <quill-editor style="height:100%" v-model="form.answer"></quill-editor>
          </div> 
        </el-form-item>

        <!-- 题目备注 -->
        <el-form-item label="题目备注：">
          <el-input type="textarea"  class="select_content" v-model="form.remarks"></el-input>
        </el-form-item>

        <!-- 试题标签 -->
        <el-form-item label="试题标签：" >
          <el-select v-model="form.tags" placeholder="请选择试题标签" class="select_content">
                <el-option v-for="item, index in tagName" :key="index" :label="item" :value="item"></el-option>
              </el-select>
        </el-form-item>

      </el-form>

      <!-- 底部按钮 -->
      <el-button v-if="id === '0' || id === ':id'" class="footer" type="success" @click="addQuestion">确认提交</el-button>
      <el-button v-else type="primary" class="footer" @click="editQuestion">确认修改</el-button>

    </el-card>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects'
// import { detail: directorys } from '@/api/hmmm/directorys'
import { detail, add, update } from '@/api/hmmm/questions'
import { createAPI } from '@/utils/request'
import { list } from '../../api/hmmm/companys'
import {citys, provinces} from '../../api/hmmm/citys'
export default {
  name: 'New',
  data () {
    return {
      subjectList: [], // 学科数据
      twoLevelDirectory: [], // 学科的二级目录
      tagName: [], // 学科的标签
      companysList: [], // 企业的所有数据
      options: [], // 答案的数据列表
      checkboxItem: [], // 默认被选中的答案
      form: {
        id: '',
        subjectID: '', // 学科的相关数据
        catalogID: 0, // 二级目录的数据
        direction: '', // 方向
        questionType: '1', // 试题类型
        difficulty: '1', // 难度
        question: '', // 题干---富文本
        enterpriseID: '', // 企业
        tags: '', // 试题标签
        remarks: '', // 题目备注
        answer: '', // 答案解析
        videoURL: '', // 解析视频
        province: '', // 城市
        city: '', // 地区
        name: '',
        options: [] // 选项
      },
      radio: 3,
      provinces: provinces(), // 市
      // provincesName: '',
      citys: [], // 县
      // 验证规则
      formRules: {
        // 答案解析
        answer: [
          { required: true, message: '请选择', trigger: 'blur' }
        ],
        // 学科的验证规则
        subjectID: [
          { required: true, message: '请选择学科', trigger: 'blur' }
        ],
        // 目录的验证规则
        // catalogID: [
        //   { required: true, message: '请选择学科', trigger: 'blur' }
        // ],
        // 方向的验证规则
        direction: [
          { required: true, message: '请选择方向', trigger: 'blur' }
        ],
        // 企业的验证规则
        enterpriseID: [
          { required: true, message: '请选择方向', trigger: 'blur' }
        ],
        // 城市的验证规则
        province: [
          { required: true, message: '请选择城市', trigger: 'blur' }
        ],
        // 题型的验证规则
        questionType: [
          { required: true, message: '请选择题型', trigger: 'blur' }
        ],
        // 题干的验证规则
        question: [
          { required: true, message: '请选择题干', trigger: 'blur' }
        ],
        // 难度的验证规则
        difficulty: [
          { required: true, message: '请选择难度', trigger: 'blur' }
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
      ],
      // 选项
      optionCheckbox: [
        {
          id: 1,
          code: 'A：',
          title: '',
          img: '',
          isRight: false
        },
        {
          id: 2,
          code: 'B：',
          title: '',
          img: '',
          isRight: false
        },
        {
          id: 3,
          code: 'C：',
          title: '',
          img: '',
          isRight: false
        },
        {
          id: 4,
          code: 'D：',
          title: '',
          img: '',
          isRight: false
        }
      ]
    }
  },
  props: {
    id: {
      type: [ String, Number, Object],
      required : true,
      default: 0
    }
  },
  created () {
      // 获取学科列表
      this.getsubject()
      // 获取企业数据列表、
      this.getcompanys()
      // 根据id获取当前这一条数据的所有内容
      // console.log(this.id)
      if (this.id !== '0' && this.id !== ':id') {
        console.log(111)
        this.getIdList()
      }
      // console.log(this.id)
  },
  methods: {
    // 获取学科列表
    async getsubject () {
      try {
        const { data } = await simple()
        // console.log(data)
        this.subjectList = data
      } catch (err) {
        // this.$message.error('获取数据失败')
      }
    },
    // 学科的change事件，获取二级目录
    async selectCatalogue () {
      this.tagName = []
      try {
        // 获取学科二级目录的请求
        console.log(this.form.subjectID)
        const { data } = await createAPI(`/directorys/${this.form.subjectID}`, 'get')
        // const { data } = await detail(id)
        console.log(data)
        this.twoLevelDirectory = data.twoLevelDirectory

        // 获取当前试题标签的请求
        const res = await createAPI('/tags/' + this.form.subjectID, 'get')
        console.log(res)
        this.tagName.push(res.data.tagName)

      } catch (err) {
      }
    },
    // 获取企业的数据别表
    async getcompanys () {
      try {
        // console.log('企业')
        const query = {
          page: 1,
          pagesize: 10
        }
        const { data} = await list(query)
        console.log(data)
        this.companysList = data.items
      } catch (err) {
        this.$message.error('获取数据失败')
      }
    },
    // 根据id获取当前这一条数据的所有内容
    async getIdList () {
      try {
        const { data } = await detail({id: this.id})
        console.log(data)
        // 把获取到的数据给form
        this.form.remarks = data.remarks
        this.form.answerID = data.answerID
        this.form.subjectID = data.subjectID, // 学科的相关数据
        this.form.catalogID = data.catalogID, // 二级目录的数据
        this.form.direction = data.direction, // 方向
        this.form.questionType = data.questionType, // 试题类型
        this.form.difficulty = data.difficulty, // 难度
        this.form.question = data.question, // 题干---富文本
        this.form.creatorID = data.creatorID, // 企业
        this.form.tags = data.tags, // 学科的标签
        this.form.remarks = data.remarks, // 题目备注
        this.form.answer = data.answer // 答案解析
        this.form.videoURL = data.videoURL // 解析视频

        // 获取到的所有答案选项
        this.options = data.options
        console.log(this.options)
        const checkboxItem = this.options.filter(item => {
          return item.isRight === 1
        })
        console.log(checkboxItem);
        // this.checkboxItem = checkboxItem
        const newCheckboxItem = checkboxItem.map(function (arr) {
          return arr.title
        })
        // console.log(newCheckboxItem)
        this.checkboxItem = newCheckboxItem
      } catch (err) {
        // this.$message.error('获取数据失败')
      }
    },
    // 选择城市下拉框的change事件
    selectProvinces (provinces) {
      // console.log(provinces)
      this.citys = []
      const res =  citys(provinces)
      this.citys = res
    },
    // 新增题的按钮
    async addQuestion () {
      this.$refs.addFormRef.validate(async valid => {
        // 失败的情况直接return
        // console.log(valid);
        if (!valid) return this.$message.error('请将必填项填写完成！')
        // 成功之后再发起请求
      try {
        const { data } = await add(this.form)
        console.log(data)
        this.$message.success('新增题库成功')
      } catch (err) {
        console.dir(err)
        this.$message.error('新增题库失败')
      }
      })
    },
    // 修改题的按钮
    async editQuestion () {
      this.$refs.addFormRef.validate(async valid => {
        // 失败的情况直接return
        console.log(valid);
        if (!valid) return this.$message.error('请将必填项填写完成！')
        // 成功之后再发起请求
        this.form.id = parseInt(this.id)
      try {
        const { data } = await update(this.form)
        this.$router.push('/questions/list')
        console.log(data)
      } catch (err) {
        console.dir(err)
        this.$message.error('新增题库失败')
      }
      })
    },
    // 点击添加选项按钮时添加一个选项
    addOptions () {
      const list = {
          id: 4,
          code: 'D：',
          title: '',
          img: '',
          isRight: false
        }
      this.optionCheckbox.push(list)
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
.optionWidth {
  width: 600px;
}

.footer {
  margin-left: 140px;
}
</style>
