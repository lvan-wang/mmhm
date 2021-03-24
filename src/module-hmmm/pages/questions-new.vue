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
        <el-form-item label="目录：" prop="catalogID">
          <el-select v-model="form.catalogID" placeholder="请选择目录" class="select_content">
            <el-option v-for="item, index in twoLevelDirectory" :key="index" :label="item.directoryName" :value="item.id"></el-option>
          </el-select>
        </el-form-item>

        <!-- 企业 -->
        <el-form-item label="企业：" prop="enterpriseID">
          <el-select v-model="form.enterpriseID" placeholder="请选择企业" class="select_content">
            <el-option v-for="item, index in companysList" :key="index" :label="item.company" :value="item.id"></el-option>
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
        <!-- 选项 如果是简答题就不显示选项-->
        <el-form-item label="选项：" class="optionWidth" v-show="form.questionType !== '3'">
          <!-- 添加的情况 -->
          <div v-if="id === '0' || id === ':id'">
            <!-- 单选题 -->
            <div v-if="form.questionType === '1'">
             <el-radio-group v-model="optionsNum">

              <div class="block" v-for="item, index in optionCheckbox" :key="index" >
                <el-radio :label="item.id">{{item.code}}</el-radio>
                <el-input v-model="item.title"></el-input>
                 <!-- 上传 -->
                    <el-upload
                      class="avatar-uploader"
                      action="https://www.liulongbin.top:8888/api/private/v1/upload"
                      :headers='headerObj'
                      :on-success="(res, file) => { updateImg(item, res, file) }"
                      :show-file-list="false"
                      :on-remove="handleRemove"
                      >
                      
                        <img v-if="item.img" :src="item.img" class="avatar">
                      <el-button v-else class="onpicBtn" >上传图片
                        <i class="el-icon-circle-close icon_close"></i>
                      </el-button>
                    </el-upload>
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
              <el-checkbox-group v-model="optionsArr">
                <div class="block">
                    <el-checkbox :label="item.code" style="width:80px"
                    @click="item.isRight = !item.isRight"
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

        <!-- 试题标签 multiple -->
        <el-form-item label="试题标签：" >
          <el-select v-model="form.tags"  placeholder="请选择试题标签"  class="select_content">
                <el-option v-for="item, index in tagName" :key="index" :label="item.tagName" :value="item.id"></el-option>
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
import { mapState } from 'vuex'
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
        catalogID: '', // 二级目录的数据
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
      newtags: [],
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
        catalogID: [
          { required: true, message: '请选择目录', trigger: 'blur' }
        ],
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
          isRight: 0
        },
        {
          id: 2,
          code: 'B：',
          title: '',
          img: '',
          isRight: 0
        },
        {
          id: 3,
          code: 'C：',
          title: '',
          img: '',
          isRight: 0
        },
        {
          id: 4,
          code: 'D：',
          title: '',
          img: '',
          isRight: 0
        }
      ],
      newId: 5,
      newCode: 69,
      // 单选的情况
      optionsNum: '',
      // 多选的情况
      optionsArr: [],
      // 图片上传组件的请求头配置对象
      headerObj: {
        Authorization: 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjUwMCwicmlkIjowLCJpYXQiOjE2MTY1ODIyMzQsImV4cCI6MTYxNjY2ODYzNH0.t3jGjqvaPhrG-BUEo2KI4rGQtfYA7vxz4r0vnV2ykQM'
      },

    }
  },
  computed: {
    ...mapState(['token']),
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
        const { data } = await createAPI('/directorys/', 'get')
        // const { data } = await detail(id)
        console.log(data)
        this.twoLevelDirectory = data.items

        // 获取当前试题标签的请求
        // const res = await createAPI('/tags/' + this.form.subjectID, 'get')
        const res = await createAPI('/tags/', 'get')
        console.log(res)
        this.tagName = res.data.items

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
        // 判断单选或者多选的情况
        if (this.form.questionType === '1') {
          var newradio = this.optionCheckbox.find(item => {
            return item.id = this.optionsNum
          })
          console.log(newradio)

        }
        console.log(this.form.tags)
        this.form.tags = this.form.tags + ''
        console.log(this.form.tags)
        // 把数据存入form.options中
        this.form.options = this.optionCheckbox
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
      if (this.newCode > 90) {
        return this.$message.info('选项不能再多了！')
      }
      const list = {
          id: '',
          code: '',
          title: '',
          img: '',
          isRight: false
      }
      list.code = String.fromCharCode(this.newCode++)
      list.id = this.newId++
      this.optionCheckbox.push(list)

    },
    // 上传成功, 把返回的图片地址，赋值预览图片
    updateImg (item, response, file) {
      item.img = URL.createObjectURL(file.raw)
      console.log(item.img)
    },
    // 移除上传的图片
    removeImg (currentImg, index) {
      // 如果有图片，就把当前图片清空
      if (currentImg) {
        this.reqParams.options[index].img = ''
        // console.log(this.reqParams.options[index].img)
      }
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    
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
  margin-left: 10px;
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
.avatar-uploader{
  position: relative;
  vertical-align: middle;
  line-height: 1;
}
.avatar {
  margin: 0 0 0 8px;
  width: 100px;
  height: 60px;
}
</style>
