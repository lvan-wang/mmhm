<template>
  <div class='questionChoiceContainer'>
    <div class="app-container">
      <el-card shadow="never">
        <!-- 新增试题部分 -->
        <div>
          <span class="text">说明：目前支持学科和关键字条件筛选</span>
          <el-button class="filter-item fr" size="small" style="margin-left: 10px;" type="success" icon="el-icon-edit"
            @click="$router.push('/questions/new')">新增试题</el-button>
        </div>
        <!-- /新增试题部分 -->
        <!-- 表单部分 -->
        <el-form :inline="true" :model="queryInfo" ref="queryInfoRef" class="demo-form-inline">
          <el-form-item label="学科" prop="subjectID">
            <el-select @change="selectCatalogue" v-model="queryInfo.subjectID" placeholder="请选择">
              <el-option v-for="(item, index) in subjectList" :key="index" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="二级目录" prop="catalogID">
            <el-select v-model="queryInfo.catalogID" placeholder="请选择">
              <el-option v-for="(item, index) in menuList" :key="index" :label="item.directoryName" :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="标签" prop="tags">
            <el-select v-model="queryInfo.tags" placeholder="请选择">
              <el-option v-for="(item, index) in tagsList" :key="index" :label="item.tagName" :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="关键字" prop="keyword">
            <el-input v-model="queryInfo.keyword" placeholder="根基题干搜索"></el-input>
          </el-form-item>
          <el-form-item label="试题类型" prop="questionType">
            <el-select v-model="queryInfo.questionType" placeholder="请选择">
              <el-option v-for="item in questionTypeList" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="难度" prop="difficult">
            <el-select v-model="queryInfo.difficult" placeholder="请选择">
              <el-option v-for="item in difficultList" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="方向" prop="direction">
            <el-select v-model="queryInfo.direction" placeholder="请选择">
              <el-option v-for="item in directionList" :key="item.value" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="录入人" prop="creatorID">
            <el-select v-model="queryInfo.creatorID" placeholder="请选择">
              <el-option v-for="item in setRecords" :key="item.value" :label="item.username" :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="题目备注" prop="remarks">
            <el-input v-model="queryInfo.remarks" placeholder="请输入内容"></el-input>
          </el-form-item>
          <el-form-item label="企业简称" prop="shortName">
            <el-input v-model="queryInfo.shortName" placeholder="请输入内容"></el-input>
          </el-form-item>
          <el-form-item label="城市" prop="city">
              <el-cascader v-model="queryInfo.city" :options="cityData" style="width:100%"></el-cascader>
          </el-form-item>
          <div class="btn">
            <el-form-item>
              <el-button size="mini" @click="resetForm">清除</el-button>
              <el-button type="primary" size="mini" @click="handleFilter">搜索</el-button>
            </el-form-item>
          </div>
        </el-form>
        <!-- /表单部分 -->

        <!-- TAB栏切换 -->
        <div class="tab">
          <el-tabs v-model="activeName" type="card" @tab-click="handleClick">
            <el-tab-pane label="全部" name="first">
              <!-- 数据记录 -->
              <el-alert :title="'数据一共'+total+'条'" type="info" show-icon :closable="false">
              </el-alert>
              <!-- /数据记录 -->
              <!-- 表格区域 -->
              <el-table :data="tableList"  style="width: 100%">
                <el-table-column prop="number" label="试题编号" width="150">
                </el-table-column>
                <el-table-column prop="subject" label="学科" width="120">
                </el-table-column>
                <el-table-column prop="catalog" label="目录" width="120">
                </el-table-column>
                <el-table-column prop="questionType" label="题型" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.questionType === '1'">单选题</span>
                    <span v-else-if="scope.row.questionType === '2'">多选题</span>
                    <span v-else>简答</span>
                  </template>
                </el-table-column>
                <el-table-column prop="question" label="题干" width="300">
                  <template slot-scope="scope">
                    <span v-html="scope.row.question"></span>
                  </template>
                </el-table-column>
                <el-table-column prop="addDate" label="录入时间" width="180">
                  <template slot-scope="scope">
                    <span>{{scope.row.addDate | parseTime}}</span>
                  </template>
                </el-table-column>
                <el-table-column prop="difficulty" label="难度" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.difficulty === '1'">简单</span>
                    <span v-else-if="scope.row.difficulty === '2'">一般</span>
                    <span v-else>困难</span>
                  </template>
                </el-table-column>
                <el-table-column prop="creator" label="录入人" width="120">
                </el-table-column>
                <el-table-column prop="chkState" label="审核状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.chkState === 0">等待审核</span>
                    <span v-else-if="scope.row.chkState === 1">审核已通过</span>
                    <span v-else>审核已拒绝</span>
                  </template>
                </el-table-column>
                <el-table-column prop="chkRemarks" label="审核意见" width="120">
                </el-table-column>
                <el-table-column prop="chkUser" label="审核人" width="120">
                </el-table-column>
                <el-table-column prop="publishState" label="发布状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.publishState === 1">上架</span>
                    <span v-else-if="scope.row.publishState === 0">下架</span>
                    <span v-else>错误</span>
                  </template>
                </el-table-column>
                <el-table-column fixed="right" label="操作" width="210">
                  <template slot-scope="scope">
                    <el-button @click="preview(scope.row)" type="text" size="small">预览</el-button>
                    <el-button type="text" size="small">审核</el-button>
                    <el-button type="text" size="small" @click="$router.push(`/questions/new/${scope.row.id}`)">修改</el-button>
                    <el-button type="text" size="small">上架</el-button>
                    <el-button type="text" size="small">删除</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <!-- 分页 -->
              <div class="pagination">
                 <el-pagination
                   background
                   @size-change="handleSizeChange"
                   @current-change="handleCurrentChange"
                   :current-page="queryInfo.page"
                   :page-sizes="[5, 10, 15, 20]"
                   :page-size="queryInfo.pagesize"
                   layout="total, sizes, prev, pager, next, jumper"
                   :total="total">
                   </el-pagination>
              </div>
              <!-- /分页 -->
              <!-- /表格区域 -->
            </el-tab-pane>
            <el-tab-pane label="待审核" name="second">
              <!-- 数据记录 -->
              <el-alert :title="'数据一共'+totalUncheck+'条'" type="info" show-icon :closable="false">
              </el-alert>
              <!-- /数据记录 -->
              <!-- 表格区域 -->
              <el-table :data="tableUncheckList"  style="width: 100%">
                <el-table-column prop="number" label="试题编号" width="150">
                </el-table-column>
                <el-table-column prop="subject" label="学科" width="120">
                </el-table-column>
                <el-table-column prop="catalog" label="目录" width="120">
                </el-table-column>
                <el-table-column prop="questionType" label="题型" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.questionType === '1'">单选题</span>
                    <span v-else-if="scope.row.questionType === '2'">多选题</span>
                    <span v-else>简答</span>
                  </template>
                </el-table-column>
                <el-table-column prop="question" label="题干" width="300">
                  <template slot-scope="scope">
                    <span v-html="scope.row.question"></span>
                  </template>
                </el-table-column>
                <el-table-column prop="addDate" label="录入时间" width="180">
                  <template slot-scope="scope">
                    <span>{{scope.row.addDate | parseTime}}</span>
                  </template>
                </el-table-column>
                <el-table-column prop="difficulty" label="难度" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.difficulty === '1'">简单</span>
                    <span v-else-if="scope.row.difficulty === '2'">一般</span>
                    <span v-else>困难</span>
                  </template>
                </el-table-column>
                <el-table-column prop="creator" label="录入人" width="120">
                </el-table-column>
                <el-table-column prop="chkState" label="审核状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.chkState === 0">等待审核</span>
                    <span v-else-if="scope.row.chkState === 1">审核已通过</span>
                    <span v-else>审核已拒绝</span>
                  </template>
                </el-table-column>
                <el-table-column prop="chkRemarks" label="审核意见" width="120">
                </el-table-column>
                <el-table-column prop="chkUser" label="审核人" width="120">
                </el-table-column>
                <el-table-column prop="publishState" label="发布状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.publishState === 1">上架</span>
                    <span v-else-if="scope.row.publishState === 0">下架</span>
                    <span v-else>错误</span>
                  </template>
                </el-table-column>
                <el-table-column fixed="right" label="操作" width="210">
                  <template slot-scope="scope">
                    <el-button @click="preview(scope.row)" type="text" size="small">预览</el-button>
                    <el-button type="text" size="small">审核</el-button>
                    <el-button type="text" size="small">修改</el-button>
                    <el-button type="text" size="small">上架</el-button>
                    <el-button type="text" size="small">删除</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <!-- 分页 -->
              <div class="pagination">
                 <el-pagination
                   background
                   @size-change="handleSizeChange"
                   @current-change="handleCurrentChange"
                   :current-page="queryInfo.page"
                   :page-sizes="[5, 10, 15, 20]"
                   :page-size="queryInfo.pagesize"
                   layout="total, sizes, prev, pager, next, jumper"
                   :total="totalUncheck">
                   </el-pagination>
              </div>
              <!-- /分页 -->
              <!-- /表格区域 -->
            </el-tab-pane>
            <el-tab-pane label="已审核" name="third">
              <!-- 数据记录 -->
              <el-alert :title="'数据一共'+totalCheck+'条'" type="info" show-icon :closable="false">
              </el-alert>
              <!-- /数据记录 -->
              <!-- 表格区域 -->
              <el-table :data="tableCheckList"  style="width: 100%">
                <el-table-column prop="number" label="试题编号" width="150">
                </el-table-column>
                <el-table-column prop="subject" label="学科" width="120">
                </el-table-column>
                <el-table-column prop="catalog" label="目录" width="120">
                </el-table-column>
                <el-table-column prop="questionType" label="题型" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.questionType === '1'">单选题</span>
                    <span v-else-if="scope.row.questionType === '2'">多选题</span>
                    <span v-else>简答</span>
                  </template>
                </el-table-column>
                <el-table-column prop="question" label="题干" width="300">
                  <template slot-scope="scope">
                    <span v-html="scope.row.question"></span>
                  </template>
                </el-table-column>
                <el-table-column prop="addDate" label="录入时间" width="180">
                  <template slot-scope="scope">
                    <span>{{scope.row.addDate | parseTime}}</span>
                  </template>
                </el-table-column>
                <el-table-column prop="difficulty" label="难度" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.difficulty === '1'">简单</span>
                    <span v-else-if="scope.row.difficulty === '2'">一般</span>
                    <span v-else>困难</span>
                  </template>
                </el-table-column>
                <el-table-column prop="creator" label="录入人" width="120">
                </el-table-column>
                <el-table-column prop="chkState" label="审核状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.chkState === 0">等待审核</span>
                    <span v-else-if="scope.row.chkState === 1">审核已通过</span>
                    <span v-else>审核已拒绝</span>
                  </template>
                </el-table-column>
                <el-table-column prop="chkRemarks" label="审核意见" width="120">
                </el-table-column>
                <el-table-column prop="chkUser" label="审核人" width="120">
                </el-table-column>
                <el-table-column prop="publishState" label="发布状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.publishState === 1">上架</span>
                    <span v-else-if="scope.row.publishState === 0">下架</span>
                    <span v-else>错误</span>
                  </template>
                </el-table-column>
                <el-table-column fixed="right" label="操作" width="210">
                  <template slot-scope="scope">
                    <el-button @click="preview(scope.row)" type="text" size="small">预览</el-button>
                    <el-button type="text" size="small">审核</el-button>
                    <el-button type="text" size="small">修改</el-button>
                    <el-button type="text" size="small">上架</el-button>
                    <el-button type="text" size="small">删除</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <!-- 分页 -->
              <div class="pagination">
                 <el-pagination
                   background
                   @size-change="handleSizeChange"
                   @current-change="handleCurrentChange"
                   :current-page="queryInfo.page"
                   :page-sizes="[5, 10, 15, 20]"
                   :page-size="queryInfo.pagesize"
                   layout="total, sizes, prev, pager, next, jumper"
                   :total="totalCheck">
                   </el-pagination>
              </div>
              <!-- /分页 -->
              <!-- /表格区域 -->
            </el-tab-pane>
            <el-tab-pane label="已拒绝" name="fourth">
               <!-- 数据记录 -->
              <el-alert :title="'数据一共'+totalReject+'条'" type="info" show-icon :closable="false">
              </el-alert>
              <!-- /数据记录 -->
              <!-- 表格区域 -->
              <el-table :data="tableRejectList"  style="width: 100%">
                <el-table-column prop="number" label="试题编号" width="150">
                </el-table-column>
                <el-table-column prop="subject" label="学科" width="120">
                </el-table-column>
                <el-table-column prop="catalog" label="目录" width="120">
                </el-table-column>
                <el-table-column prop="questionType" label="题型" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.questionType === '1'">单选题</span>
                    <span v-else-if="scope.row.questionType === '2'">多选题</span>
                    <span v-else>简答</span>
                  </template>
                </el-table-column>
                <el-table-column prop="question" label="题干" width="300">
                  <template slot-scope="scope">
                    <span v-html="scope.row.question"></span>
                  </template>
                </el-table-column>
                <el-table-column prop="addDate" label="录入时间" width="180">
                  <template slot-scope="scope">
                    <span>{{scope.row.addDate | parseTime}}</span>
                  </template>
                </el-table-column>
                <el-table-column prop="difficulty" label="难度" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.difficulty === '1'">简单</span>
                    <span v-else-if="scope.row.difficulty === '2'">一般</span>
                    <span v-else>困难</span>
                  </template>
                </el-table-column>
                <el-table-column prop="creator" label="录入人" width="120">
                </el-table-column>
                <el-table-column prop="chkState" label="审核状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.chkState === 0">等待审核</span>
                    <span v-else-if="scope.row.chkState === 1">审核已通过</span>
                    <span v-else>审核已拒绝</span>
                  </template>
                </el-table-column>
                <el-table-column prop="chkRemarks" label="审核意见" width="120">
                </el-table-column>
                <el-table-column prop="chkUser" label="审核人" width="120">
                </el-table-column>
                <el-table-column prop="publishState" label="发布状态" width="120">
                  <template slot-scope="scope">
                    <span v-if="scope.row.publishState === 1">上架</span>
                    <span v-else-if="scope.row.publishState === 0">下架</span>
                    <span v-else>错误</span>
                  </template>
                </el-table-column>
                <el-table-column fixed="right" label="操作" width="210">
                  <template slot-scope="scope">
                    <el-button @click="preview(scope.row)" type="text" size="small">预览</el-button>
                    <el-button type="text" size="small">审核</el-button>
                    <el-button type="text" size="small">修改</el-button>
                    <el-button type="text" size="small">上架</el-button>
                    <el-button type="text" size="small">删除</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <!-- 分页 -->
              <div class="pagination">
                 <el-pagination
                   background
                   @size-change="handleSizeChange"
                   @current-change="handleCurrentChange"
                   :current-page="queryInfo.page"
                   :page-sizes="[5, 10, 15, 20]"
                   :page-size="queryInfo.pagesize"
                   layout="total, sizes, prev, pager, next, jumper"
                   :total="totalReject">
                   </el-pagination>
              </div>
              <!-- /分页 -->
              <!-- /表格区域 -->
            </el-tab-pane>
          </el-tabs>
        </div>
        <!-- /TAB栏切换 -->
      </el-card>
    </div>
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
import cityData from '../citydata'
import { simple } from '@/api/hmmm/subjects'
import { list } from '@/api/hmmm/directorys'
import { createAPI } from '@/utils/request'
import { choice } from '@/api/hmmm/questions'
import QuestionsPreview from '@/module-hmmm/components/questions-preview'

export default {
  name: 'questionChoice',
  components: {
    QuestionsPreview
  },
  data () {
    return {
      queryInfo: {
        page: 1,
        pagesize: 5,
        subjectID: '', // 学科
        tags: '', // 标签
        keyword: '', // 关键字
        questionType: '', // 试题类型
        difficult: '', // 难度
        direction: '', // 方向
        creatorID: '', // 录入人
        remarks: '', // 备注
        shortName: '', // 企业简称
        city: '', // 城市
        catalogID: '', // 目录
        chkState: '' // 审核状态
      },
      // 点击当前这一行所有的数据
      row: {},
      // 预览按钮的弹出框
      previewVisible: false,
      // 学科列表
      subjectList: [],
      // 二级目录列表
      menuList: [],
      // 三级标签列表
      tagsList: [],
      // 试题类型
      questionTypeList: [{
          value: '1',
          label: '单选'
        }, {
          value: '2',
          label: '多选'
        }, {
          value: '3',
          label: '简答'
        }
      ],
      // 难度
      difficultList: [{
          value: '1',
          label: '简单'
        }, {
          value: '2',
          label: '一般'
        }, {
          value: '3',
          label: '困难'
        }
      ],
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
      // 录入人数据
      setRecords: [],
      // 城市
      cityData: cityData,
      // tab栏切换
      activeName: 'first',
      // 表格区域数据
      tableList: [],
      // 数据总条数
      total: null,
      tableUncheckList: [],
      totalUncheck: null,
      tableCheckList: [],
      totalCheck: null,
      tableRejectList: [],
      totalReject: null,
    }
  },
  computed: {},
  created () {
    // 获取全部试题列表
    this.getList()
    // 获取学科列表
    this.getSubjectList()
    // 获取录入人列表
    this.setRecordsList()
    this.getUncheckList()
    this.getCheckList()
    this.getRejectList()
  },
  mounted () {},
  methods: {
    // 获取全部试题列表
    async getList () {
      const query = {
            page: this.queryInfo.page,
            pagesize: this.queryInfo.pagesize
          }
      try {
        const { data } = await choice(query)
        console.log(data)
        this.tableList = data.items
        this.total = data.counts
      } catch (err) {
        this.$message.error('获取数据失败！')
      }
    },
    // 获取待审核试题列表
    async getUncheckList () {
      const query = {
            chkState: 0
          }
      try {
        const { data } = await choice(query)
        console.log(data)
        this.tableUncheckList = data.items
        this.totalUncheck = data.counts
      } catch (err) {
        this.$message.error('获取数据失败！')
      }
    },
    // 获取审核通过试题列表
    async getCheckList () {
      const query = {
            chkState: 1
          }
      try {
        const { data } = await choice(query)
        console.log(data)
        this.tableCheckList = data.items
        this.totalCheck = data.counts
      } catch (err) {
        this.$message.error('获取数据失败！')
      }
    },
    // 获取审核拒绝试题列表
    async getRejectList () {
      const query = {
            chkState: 2
          }
      try {
        const { data } = await choice(query)
        console.log(data)
        this.tableRejectList = data.items
        this.totalReject = data.counts
      } catch (err) {
        this.$message.error('获取数据失败！')
      }
    },
    // 获取学科列表
    async getSubjectList () {
      const { data } = await simple()
      console.log(data)
      this.subjectList = data
    },
    // 获取二级目录列表
    async selectCatalogue () {
      try {
        console.log(this.queryInfo.subjectID)
        const data = {subjectID: this.queryInfo.subjectID}
        const { data: res } = await list(data)
        console.log(res)
        this.menuList = res.items
      } catch (err) {
        this.$message.error('获取二级目录失败')
      }
      try {
        console.log(this.queryInfo.subjectID)
        const data = {subjectID: this.queryInfo.subjectID}
        const { data: res } = await createAPI('/tags', 'get', data)
        console.log(res.items)
        // const arr = res.items
        // const val = arr.map(item => item.tagName)
        // console.log(val)
        // this.tagsList = val
        this.tagsList = res.items
      } catch (err) {
        this.$message.error('获取标签失败')
      }
    },
    // 获取录入人列表
    async setRecordsList () {
      try {
        const {data} = await createAPI('/users/', 'get', {
          page: 1,
          pagesize: 10
        })
        // console.log(data)
        this.setRecords = data.list
      } catch (err) {
        this.$message.error('获取数据失败')
      }
    },
    // 重置搜索查找列表区域
    resetForm () {
      console.log(111)
      this.$refs.queryInfoRef.resetFields()
    },
    // 根据条件搜索
    async handleFilter () {
      try {
        const { data } = await choice(this.queryInfo)
        console.log(data)
        this.tableList = data.items
        this.total = data.counts
      } catch (err) {
        this.$message.error('获取数据失败')
      }
    },
    // 预览功能的按钮
    preview (row) {
      this.previewVisible = true
      console.log(row)
      this.row = row
    },
    // 监听页容量pagesize变化
    handleSizeChange(newSize) {
      console.log(`每页 ${newSize} 条`)
      this.queryInfo.pagesize = newSize
      this.getList()
    },
    // 监听当前页码值变化
    handleCurrentChange(newPage) {
      console.log(`当前页: ${newPage}`)
      this.queryInfo.page = newPage
      this.getList()
    },
    // tab栏切换
    handleClick(tab, event) {
      console.log(tab, event);
    },
    // 点击修改，路由跳转
    // jump (id) {
    //   console.log(id)
    //   this.$router.push({path:'new', params:{id:id}})
    // }
  }
}
</script>

<style scoped lang='less'>
.questionChoiceContainer {
  .text {
    font-size: 12px;
    color: #FFC0CB;
  }
  .el-form {
    margin-top: 20px;
    .el-form-item {
      padding: 0 15px;
    }
    .btn {
      float: right;
    }
  }
  .tab {
    margin-top: 10px;
    .el-table {
      margin-top: 10px;
    }
  }
  .pagination {
    float: right;
    padding-top: 20px;
    height: 60px;
  }
}
</style>
