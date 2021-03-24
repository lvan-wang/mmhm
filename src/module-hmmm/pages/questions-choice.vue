<template>
  <div class='choice-page'>
    <el-card class="box-card">
      <el-row type="flex" justify="space-between" style="margin-bottom: 20px;">
        <el-col :span="6">
          <span class="hd-txt">说明：目前支持学科和关键字条件筛选</span>
        </el-col>
        <el-col :span="6" style="text-align: right;">
          <el-button @click="$router.push('/questions/new/:id')" type="success" icon="el-icon-edit" size="small">新增试题</el-button>
        </el-col>
      </el-row>
      <el-form ref="choiceForm" :model="reqParams" label-width="80px" size="small">
        <el-row>
          <el-col :span="6">
            <el-form-item label="学科" prop="subjectID">
              <el-select v-model="reqParams.subjectID" @change="changeSubject" placeholder="请选择学科" style="width: 100%">
                <el-option v-for="item in subjectList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="二级目录" prop="catalogID">
              <el-select v-model="reqParams.catalogID" placeholder="请选择目录" style="width: 100%">
                <el-option v-for="item in directoryList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="标签" prop="tags">
              <el-select v-model="reqParams.tags" placeholder="请选择标签" style="width: 100%">
                <el-option v-for="item in tagList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="关键字" prop="keyword">
              <el-input v-model="reqParams.keyword" placeholder="请输入关键字"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="6">
            <el-form-item label="试题类型" prop="questionType">
              <el-select v-model="reqParams.questionType" placeholder="请选择试题类型" style="width: 100%">
                <el-option v-for="item in questionTypeList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="难度" prop="difficulty">
              <el-select v-model="reqParams.difficulty" placeholder="请选择难度" style="width: 100%">
                <el-option v-for="item in difficultyList" :key="item.value" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="方向" prop="direction">
              <el-select v-model="reqParams.direction" placeholder="请选择方向" style="width: 100%">
                <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="录入人" prop="creatorID">
              <el-select v-model="reqParams.creatorID" placeholder="请选择录入人" style="width: 100%">
                <el-option v-for="item in userList" :key="item.id" :label="item.username" :value="item.id"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="6">
            <el-form-item label="题目备注" prop="remarks">
              <el-input v-model="reqParams.remarks" placeholder="请输入题目备注"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="企业简称" prop="shortName">
              <el-input v-model="reqParams.shortName" placeholder="请输入企业简称"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="城市" prop="province">
              <el-row type="flex" justify="space-between">
                <el-col :span="12">
                  <el-select v-model="reqParams.province" @change="changeProvince" placeholder="请选择" style="width: 100%;">
                    <el-option v-for="item in provinceList" :key="item" :label="item" :value="item"></el-option>
                  </el-select>
                </el-col>
                <el-col :span="11">
                   <el-form-item prop="city">
                      <el-select v-model="reqParams.city" placeholder="请选择" style="width: 100%">
                        <el-option v-for="item in cityList" :key="item" :label="item" :value="item"></el-option>
                      </el-select>
                   </el-form-item>
                </el-col>
              </el-row>
            </el-form-item>
          </el-col>
          <el-col :span="6" style="text-align: right;">
            <el-form-item>
              <el-button @click="resetBtn">清除</el-button>
              <el-button @click="searchBtn" type="primary">搜索</el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <el-tabs v-model="reqParams.chkState" type="card" @tab-click="clickSwitchState">
        <el-tab-pane label="全部" name="all"></el-tab-pane>
        <el-tab-pane label="待审核" name="0"></el-tab-pane>
        <el-tab-pane label="已审核" name="1"></el-tab-pane>
        <el-tab-pane label="已拒绝" name="2"></el-tab-pane>
      </el-tabs>
      <el-alert
        style="margin-bottom: 20px"
        :title="`数据一共 ${total} 条`"
        type="info"
        show-icon
        :closable="false">
      </el-alert>
      <!-- 表格 -->
      <el-table :data="tableData" style="width: 100%">
        <el-table-column label="试题编号" prop="number" width="120"></el-table-column>
        <el-table-column label="学科" prop="subject" width="100"></el-table-column>
        <el-table-column label="目录" prop="catalog" width="100"></el-table-column>
        <el-table-column label="题型" width="100">
          <template slot-scope="scope">
            <div>{{ scope.row.questionType === '1' ? '单选' : scope.row.questionType === '2' ? '多选' : '简答'}}</div>
          </template>
        </el-table-column>
        <el-table-column label="题干" width="300">
          <template slot-scope="scope">
            <div v-html="scope.row.question"></div>
          </template>
        </el-table-column>
        <el-table-column label="录入时间" prop="addDate" width="180">
          <template slot-scope="scope">
              <span>{{scope.row.addDate | parseTimeByString }}</span>
            </template>
        </el-table-column>
        <el-table-column label="难度" width="100">
          <template slot-scope="scope">
            <div>{{ scope.row.difficulty === '1' ? '简单' : scope.row.difficulty === '2' ? '一般' : '困难'}}</div>
          </template>
        </el-table-column>
        <el-table-column label="录入人" prop="creator" width="120"></el-table-column>
        <el-table-column label="审核状态" width="100">
          <template slot-scope="scope">
            {{ chkTypeList.find(item => item.value === scope.row.chkState + 1).label }}
          </template>
        </el-table-column>
        <el-table-column label="审核意见" prop="chkRemarks" width="100"></el-table-column>
        <el-table-column label="审核人" prop="chkUser" width="100"></el-table-column>
        <el-table-column label="发布状态" width="100">
          <template slot-scope="scope">
            <!-- {{ publishTypeList.find(item => item.value === scope.row.publishState + 1).label }} -->
            <span v-if="scope.row.chkState === 1 && scope.row.publishState === 1">{{ '已发布' }}</span>
            <span v-else-if="scope.row.chkState === 1 && scope.row.publishState === 0">{{ '已下架' }}</span>
            <span v-else>{{ '待发布' }}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作" fixed="right" width="220" align="center">
          <template slot-scope="scope">
            <el-button @click="preview(scope.row)" type="text" size="mini">预览</el-button>
            <el-button @click="auditOpen(scope.row.id)" type="text" :disabled="scope.row.chkState !== 0" size="mini">审核</el-button>
            <el-button @click="$router.push(`/questions/new/${scope.row.id}`)" type="text" :disabled="scope.row.publishState === 1" size="mini">修改</el-button>
            <el-button @click="isPutaway(scope.row)" type="text" size="mini">{{ scope.row.publishState === 1 ? '下架' : '上架' }}</el-button>
            <el-button @click="removeQuestions(scope.row.id)" type="text" :disabled="scope.row.publishState === 1" size="mini">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-pagination
        style="margin: 20px; float: right"
        background
        @current-change="changePage"
        @size-change="changeSize"
        :current-page="reqParams.page"
        :page-sizes="[5, 10, 15, 20]"
        :page-size="reqParams.pagesize"
        layout="prev, pager, next, sizes, jumper"
        :total="total">
      </el-pagination>
    </el-card>
    <audit ref="Audit" :auditId="auditId" @approve="approve"></audit>
    <!-- 预览弹层组件 -->
    <!-- <preview ref="Preview" :previewId="previewId"></preview> -->
    <!-- 预览弹出层 -->
    <el-dialog
      title="题目预览"
      :visible.sync="previewVisible"
      width="60%"
      >
      <span>
        <!-- 子组件 -->
        <questions-preview :row='row'></questions-preview>
      </span>
      <div class="close">
        <el-button type="primary" @click="previewVisible = false" >关闭</el-button>
      </div>
    </el-dialog>
    <!-- /预览弹出层 -->
  </div>
</template>

<script>
// 题库接口
import { choice as choiceQuestion, remove as removeQuestion, choicePublish } from '@/api/hmmm/questions'
// 学科接口
import { simple as simpleSubject } from '@/api/hmmm/subjects'
// 目录接口
import { simple as simpleDirectory } from '@/api/hmmm/directorys'
// 标签接口
import { simple as simpleTag } from '@/api/hmmm/tags'
// 用户接口
import { simple as simpleUser } from '@/api/base/users'
// 城市数据
import { provinces, citys } from '@/api/hmmm/citys'
// 引入相关数据 难度，题型，方向, 审核
import { difficulty as topicDifficulty, questionType as topicQuestionType, direction as topicDirection, chkType, publishType } from '@/api/hmmm/constants'
// 引入预览弹层组件
import QuestionsPreview from '@/module-hmmm/components/questions-preview'
// import Preview from '../components/questions-preview'
// 引入审核对话框组件
import Audit from '../components/questions-check'
export default {
  name: 'QuestionsChoice',
  components: {
    Audit,
    QuestionsPreview
    // Preview
  },
  data () {
    return {
      reqParams: {
        // 学科
        subjectID: null,
        // 目录
        catalogID: null,
        // 标签
        tags: null,
        // 关键字
        keyword: null,
        // 试题类型
        questionType: null,
        // 难度
        difficulty: null,
        // 方向
        direction: null,
        // 录入人
        creatorID: null,
        // 题目备注
        remarks: null,
        // 企业简称
        shortName: null,
        // 城市
        province: null,
        // 城市下的地区
        city: null,
        // 审核状态
        chkState: 'all',
        page: 1,
        pagesize: 5
      },
      // 总数据
      total: 0,
      // 表格数据
      tableData: [],
      // 学科列表
      subjectList: [],
      // 目录列表
      directoryList: [],
      // 标签列表
      tagList: [],
      // 题型列表
      questionTypeList: topicQuestionType,
      // 难度列表
      difficultyList: topicDifficulty,
      // 方向列表
      directionList: topicDirection,
      // 审核状态
      chkTypeList: chkType,
      // 发布状态
      publishTypeList: publishType,
      // 用户列表
      userList: [],
      // 城市列表
      provinceList: provinces(),
      // 城市旗下的地区列表
      cityList: [],
      // 传给审核子组件的id
      auditId: null,
      // 传给预览组件的id
      // previewId: null
      // 预览按钮的弹出框
      previewVisible: false,
      row: {}
    }
  },
  mounted () {
    // 获取学科
    this.getsubject()
    // 获取用户
    this.getUser()
    // 精选题库列表
    this.getChoiceQuestion()
  },
  // computed: {
  //   getChkState () {
  //     return function (chkState) {
  //       return this.chkTypeList.find(item => {
  //         if (item.value === chkState + 1) {
  //           if (item.value === 2 && item.value === 3) {
  //             return '已' + item.label
  //           }
  //           return item.label
  //         }
  //       })
  //     }
  //   }
  // },
  methods: {
    // 获取精选题库列表数据
    async getChoiceQuestion () {
      const paramsObj = { ...this.reqParams }
      if (paramsObj.chkState === 'all') paramsObj.chkState = null
      const { data } = await choiceQuestion(paramsObj)
      this.tableData = data.items
      this.total = data.counts
    },
    // 获取学科
    async getsubject () {
      const { data } = await simpleSubject()
      this.subjectList = data
    },
    // 根据学科改变来渲染目录和标签
    async changeSubject (currentSubject) {
      // 每次学科改变就重置目录和标签
      this.reqParams.catalogID = null
      this.reqParams.tags = null
      // 目录
      const { data } = await simpleDirectory({ subjectID: currentSubject })
      this.directoryList = data
      // 标签
      const res = await simpleTag({ subjectID: currentSubject })
      this.tagList = res.data
    },
    // 获取录入人
    async getUser () {
      const { data } = await simpleUser({ keyword: this.reqParams.keyword })
      this.userList = data
    },
    // 改变城市获取旗下的地区
    changeProvince (currentCity) {
      // 每次改变重置下
      this.reqParams.city = null
      this.cityList = citys(currentCity)
    },
    // 打开预览弹层
    // previewOpen (id) {
    //   this.previewId = id
    //   // console.log(this.$refs.Preview)
    //   this.$nextTick(() => {
    //     this.$refs.Preview.openPreviewDialog()
    //   })
    // },
    // 预览功能的按钮
    preview (row) {
      this.previewVisible = true
      console.log(row)
      this.row = row
    },
    // 打开审核弹层
    auditOpen (id) {
      this.auditId = id
      this.$nextTick(() => {
        this.$refs.Audit.openDialog()
      })
    },
    // 审核成功
    approve () {
      // 渲染数据
      this.getChoiceQuestion()
    },
    // 上架或者是下架
    isPutaway (row) {
      // console.log(row.id)
      // console.log(row.publishState)
      const pS = row.publishState === 0 ? 1 : 0
      const txt = row.publishState === 0 ? '上架' : '下架'
      this.$confirm(`此操作将永久${txt}该题目, 是否继续?`, '温馨提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        try {
          await choicePublish({ id: row.id, publishState: pS })
          this.$message.success(`${txt}成功!`)
          this.getChoiceQuestion()
        } catch (e) {
          this.$message.error(`${txt}失败!`)
        }
      }).catch(() => {

      })
    },
    // 删除试题
    removeQuestions (id) {
      this.$confirm('此操作将永久删除该题目, 是否继续?', '温馨提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        try {
          await removeQuestion({ id })
          this.$message.success('删除成功!')
          this.getChoiceQuestion()
        } catch (e) {
          this.$message.error('删除失败!')
        }
      }).catch(() => {

      })
    },
    // 修改试题
    // modification (id) {
    //   this.$router.push(`/questions/new/${id}`)
    // },
    // 搜索列表,获取列表数据
    searchBtn () {
      this.getChoiceQuestion()
    },
    // 重置表单
    resetBtn () {
      this.$refs.choiceForm.resetFields()
    },
    // 点击tabs 切换
    clickSwitchState (tag) {
      console.log(tag.name)
      // 改变状态重新渲染列表
      this.reqParams.chkState = tag.name
      // 从第一页渲染
      this.reqParams.page = 1
      // 调用获取列表方法
      this.getChoiceQuestion()
    },
    // 分页
    changePage (currentPage) {
      this.reqParams.page = currentPage
      this.getChoiceQuestion()
    },
    // 每页显示的条数
    changeSize (currentSize) {
      this.reqParams.pagesize = currentSize
      // 从第一页渲染
      this.reqParams.page = 1
      this.getChoiceQuestion()
    }
  }
}
</script>

<style scoped lang='scss'>
::v-deep.el-card {
  margin: 10px;
}
.hd-txt {
  font-size: 12px;
  color: pink;
}
// ::v-deep.el-table td {
//   text-align: center;
// }
</style>
