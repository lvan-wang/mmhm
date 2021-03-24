<template>
  <div class='container'>
    <el-card>
      <!-- 头部导语和按钮 -->
      <div class="card_header">
        <span class="header_text">说明：目前支持学科和关键字条件筛选</span>
        <el-button type="success" icon="el-icon-edit" size="small" @click="$router.push('/questions/new/0')">新增试题</el-button>
      </div>
      <!-- 头部选择区域 -->
      <div class="header_select">

        <el-form ref="form" :model="form" label-width="80px">
            <!-- 第一个 -->
            <div class="header_col">
            <el-form-item label="学科">
              <el-select @change="selectCatalogue" v-model="form.subjectID" placeholder="请选择" >
                <el-option v-for="item, index in subjectList" :key="index" :value="item.value" :label="item.label"></el-option>
              </el-select>
            </el-form-item>
            </div>
          <!-- </el-form> -->
        
          <!-- 第二个 -->
          <!-- <div class="header_col"></div> -->
          <!-- <el-form ref="form" :model="form" label-width="80px"> -->
            <div class="header_col">
            <el-form-item label="二级目录">
              <el-select v-model="form.catalogID" placeholder="请选择" style="width: 100%">
                <el-option v-for="item, index in twoLevelDirectory" :key="index" :label="item" :value="item"></el-option>
              </el-select>
            </el-form-item>
            </div>
          <!-- </el-form> -->
        
          <!-- 三 -->
          <!-- <div class="header_col"> -->
          <!-- <el-form ref="form" :model="form" label-width="80px"> </el-form>-->
            <div class="header_col">
            <el-form-item label="标签">
              <el-select v-model="form.tags" placeholder="请选择" style="width: 100%">
                <el-option v-for="item, index in tagName" :key="index" :label="item" :value="item"></el-option>
              </el-select>
            </el-form-item>
            </div>
          <!-- 四 -->
          <!-- <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px"> -->
          <div class="header_col">
            <el-form-item label="关键字">
              <el-input v-model="form.keyword" placeholder="根基题干搜索"></el-input>
            </el-form-item>
          </div>
        <!-- 五 -->
        <!-- <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px"> -->
          <div class="header_col">  
            <el-form-item label="试题类型">
              <el-select v-model="form.questionType" placeholder="请选择" style="width: 100%">
                <el-option v-for="item, index in questionTypeList" :key="index" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </div>
        <!-- 六 -->
        <!-- <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px"> -->
          <div class="header_col">
            <el-form-item label="难度">
              <el-select v-model="form.difficulty" placeholder="请选择" style="width: 100%">
                <el-option v-for="item, index in regionList" :key="index" :label="item.label" :value="item.value"></el-option>
              </el-select>
            </el-form-item>
          </div>
        <!-- 七 -->
        <!-- <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px"> -->
          <div class="header_col">
            <el-form-item label="方向">
              <el-select v-model="form.direction" placeholder="请选择" style="width: 100%">
                <el-option v-for="item, index in directionList" :key="index" :label="item.label" :value="item.label"></el-option>
              </el-select>
            </el-form-item>
        </div>
        <!-- 八 -->
        <!-- <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px"> -->
            <div class="header_col">
            <el-form-item label="录入人">
              <el-select v-model="form.creatorID" placeholder="请选择" style="width: 100%">
                <el-option v-for="item, index in setRecords" :key="index" :label="item.username" :value="item.id"></el-option>
              </el-select>
            </el-form-item>
        </div>
        <!-- 九 -->
        <!-- <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px"> -->
            <div class="header_col">
            <el-form-item label="题目备注">
              <el-input v-model="form.remarks"></el-input>
            </el-form-item>
        </div>
        <!-- 十 -->
        <!-- <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px"> -->
            <div class="header_col">
            <el-form-item label="企业简称">
              <el-input v-model="form.shortName"></el-input>
            </el-form-item>
        </div>
        <!-- 十一 -->
            <div class="header_col">
            <!-- <el-form-item label="城市">
              <el-cascader v-model="form.city" :options="cityData" style="width:100%"></el-cascader>
            </el-form-item> -->

              <el-form-item label="城市：">
                <el-select @change="selectProvinces" v-model="form.province" placeholder="请选择" style="width:110px;">
                  <el-option
                  v-for="item, index in provinces"
                  :key="index"
                  :label="item"
                  :value="item">
                </el-option>
                </el-select>
                <el-select v-model="form.city" placeholder="请选择"  style="width:110px;">
                <el-option
                  v-for="item, index in citys"
                  :key="index"
                  :label="item"
                  :value="item">
                </el-option>
              </el-select>
              </el-form-item>
            </div>
        </el-form>
        
        <!-- 十二 两个按钮 -->
        <div class="header_col">
          <el-button class="col_button" size="mini" type="primary" @click="search">搜索</el-button>
          <el-button class="col_button" size="mini" @click="emptyForm">清除</el-button>
        </div>
      </div>
      <!-- 提示消息 -->
      <el-alert
        :title="'数据一共' + total +  '条'"
        type="info"
        show-icon
        :closable="false">
      </el-alert>
      <!-- 数据详细清况 -->
      <div class="body_list">
        <!-- TODO: :data="dataList" 没有绑定数据u -->
        <el-table
          :data='list'
          stripe
          style="width: 100%">
          <el-table-column label="试题编号" prop="number" width="230"></el-table-column>
          <el-table-column label="学科" prop="subject" width="50"></el-table-column>
          <el-table-column label="目录" prop="catalog" width="100"></el-table-column>
          <el-table-column label="题型" prop="questionType" width="80">
            <template slot-scope="scope">
              <span v-if="scope.row.questionType === '1'">单选题</span>
              <span v-else-if="scope.row.questionType === '2'">多选题</span>
              <span v-else>简答</span>
            </template>
          </el-table-column>
          <el-table-column label="题干">
            <template slot-scope="scope">
              <span v-html="scope.row.question"></span>
            </template>
          </el-table-column>
          <el-table-column label="录入时间" prop="addDate" width="150"></el-table-column>
          <el-table-column label="难度" prop="difficulty" width="50">
            <template slot-scope="scope">
              <span v-if="scope.row.difficulty === '1'">简单</span>
              <span v-else-if="scope.row.difficulty === '2'">一般</span>
              <span v-else>困难</span>
            </template>
          </el-table-column>
          <el-table-column label="录入人" prop="creator" width="120"></el-table-column>
          <!-- 四大按钮 -->
          <el-table-column label="操作" width="200">
            <template slot-scope="scope">
              <!-- 预览按钮 -->
              <el-button size="small" type="primary" icon="el-icon-view" :plain="true" circle @click="previewBtn(scope.row)"></el-button>
              <!-- 修改按钮 -->
              <el-button size="small" type="success" icon="el-icon-edit" :plain="true" circle @click="$router.push(`/questions/new/${scope.row.id}`)"></el-button>
              <!-- 删除按钮 -->
              <el-button size="small" type="danger" icon="el-icon-delete" :plain="true" circle @click="removeUserById(scope.row.id)"></el-button>
              <!-- 加入精选 -->
              <el-button size="small" type="warning" icon="el-icon-check" :plain="true" circle @click="toChoiceness(scope.row)"></el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页按钮 -->
        <div class="paging_btn">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="queryInfo.pagenum"
            :page-sizes="[2, 3, 5, 10]"
            :page-size="queryInfo.pagesize"
            layout=" prev, pager, next, sizes, jumper"
            :total="total">
          </el-pagination>
        </div>
      </div>
    </el-card>
    <!-- 弹出层 -->
    <el-dialog
      title="题目预览"
      :visible.sync="previewVisible"
      width="60%"
      >
      <span>
        <!-- 子组件 -->
        <questions-preview v-if="previewVisible" :row='row'></questions-preview>
      </span>
      <div class="close">
        <el-button type="primary" @click="previewVisible = false" >关闭</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
// import cityData from '../citydata'
import { list, remove, choiceAdd } from '../../api/hmmm/questions'
import { simple, detail } from '../../api/hmmm/subjects'

import { createAPI } from '@/utils/request'
import QuestionsPreview from '../components/questions-preview'
import {citys, provinces} from '../../api/hmmm/citys'
export default {
  name: 'BasicQuestions',
  components: {
    QuestionsPreview 
  },
  data () {
    return {
      list: [], // 获取到的数据列表
      total: 0, // 总数据条数
      subjectList: [], // 学科数据
      setRecords: [], // 录入人数据
      twoLevelDirectory: [], // 学科的二级目录
      tagName: [], // 学科的标签
      // form 表单提交的所有数据
      form: {
        page: 1,
        pagesize: 8,
        subjectID: '', // 学科
        keyword: '', // 关键字
        questionType: '', // 试题类型
        difficulty: '', // 难度
        direction: '', // 方向
        creatorID: '', // 录入人
        remarks: '', // 题目备注
        shortName: '', // 企业简称
        tags: [], // 标签
        province: '', // 市
        city: '', //区
        catalogID: '' // 目录
      },
      provinces: provinces(), // 市
      citys: [], // 县
      // 省市的联动
      // cityData: cityData,
      // currentPage4: 4,
      // 分页区域的参数
      queryInfo: {
        pagenum: 1,
        pagesize: 5
      },
      previewVisible: false, // 预览按钮的弹出框
      row: {}, // 点击当前这一行所有的数据
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
      regionList: [{
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
      ]
    }
  },
  created () {
    this.getList()
    // 获取学科列表
    this.getsubject()
    // 获取录入人
    this.setRecordsList()

  },
  methods: {
    // 获取数据列表
    async getList () {
      const query = {
            page: this.queryInfo.pagenum,
            pagesize: this.queryInfo.pagesize
          }
      try {
        const { data } = await list(query)
        // console.log(data)
        this.list = data.items
        this.total = data.counts
      } catch (err) {
        this.$message.error('获取数据失败！')
      }
    },
    // 点击删除按钮 发送网络请求 删除该数据
    async removeUserById (id) {
      // console.log(id);
      // 弹出确定取消框，是否删除题目
      const confirmResult = await this.$confirm('此操作将永久删除该题目, 是否继续?', '删除提示', {
        confirmButtonText: '确认删除',
        cancelButtonText: '我在想想',
        type: 'warning'
      }).catch(err => err)
      // 如果用户点击确认，则confirmResult 为'confirm'
      // 如果用户点击取消, 则confirmResult获取的就是catch的错误消息  'cancel'
      if (confirmResult !== 'confirm') {
        return this.$message.info('取消了删除')
      }
      // 发送网络请求删除该角色
      try {
        const { data } = await remove({id})
        console.log(data)
        // 删除结束后刷新页面
        this.$message.success('删除题目成功')
        this.getList()
      } catch (err) {
        this.$message.error('删除题目失败')
      }
    },
    // 预览功能的按钮
    previewBtn (row) {
      this.previewVisible = true
      // console.log(row)
      this.row = row
    },
    // 每页容量变化刷新页面
    handleSizeChange(val) {
      // console.log(`每页 ${val} 条`);
      this.queryInfo.pagesize = val
      this.getList()
    },
    // 当前页码变化更新页面
    handleCurrentChange(val) {
      // console.log(`当前页: ${val}`);
      this.queryInfo.pagenum = val
      this.getList()
    },
    // 加入精选按钮
    async toChoiceness (row) {
      console.log(row)
      const confirmResult = await this.$confirm('此操作将将该题目加入精选, 是否继续?', '提示', {
        confirmButtonText: '确认',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      // 如果用户点击确认，则confirmResult 为'confirm'
      // 如果用户点击取消, 则confirmResult获取的就是catch的错误消息  'cancel'
      if (confirmResult !== 'confirm') {
        return this.$message.info('取消了加入精选')
      }
      // 确定要加入精选 发起请求
      const data = {
        id: row.id,
        choiceState: 1
      }
      try {
        await choiceAdd(data)
        this.$message.success('加入精选成功')
        this.getList()
      } catch (err) {
        this.$message.error('加入精选失败')
      }
      
    },
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
    // 获取录入人信息
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
    // 清除按钮的点击事件
    emptyForm () {
      this.form.subjectID = '', // 学科
      this.form.keyword = '', // 关键字
      this.form.questionType = '', // 试题类型
      this.form.difficulty = '', // 难度
      this.form.direction = '', // 方向
      this.form.setRecords = '', // 录入人
      this.form.remarks = '', // 题目备注
      this.form.shortName = '', // 企业简称
      this.form.tags = '' // 标签
      this.form.province = '' // 城市
      this.form.city = '' // 城区
    },
    // 搜索按钮
    async search () {
      try {
        const { data } = await list(this.form)
        console.log(data)
        this.list = data.items
        this.total = data.counts
      } catch (err) {
        this.$message.error('获取数据失败')
      }
    },
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
    // 选择城市下拉框的change事件
    selectProvinces (provinces) {
      // console.log(provinces)
      this.citys = []
      const res =  citys(provinces)
      this.citys = res
    }
  },
  computed: {
  },
  watch: {
  }
}
</script>

<style scoped rel="stylesheet/scss" lang="scss">
.card_header {
    display: flex;
    justify-content: space-between;
    .header_text {
      font-size: 12px;
      color: pink;
    }
}
// 头部选择区域
.header_select {
  margin: 10px;
  overflow: hidden;
  .header_col {
    float: left;
    width: 25%;
    overflow: hidden;
    .col_left {
      width: 80px;
    }
    .col_button {
      float: right;
      margin-left: 5px;
    }
  }
}
// 数据列表、
.body_list {
  margin-top: 10px;
  // 分页区域
  .paging_btn {
    float: right;
    height: 60px;
    overflow: hidden;
    .el-pagination {
      padding: 20px 10px;
    }
  }
}
// 弹出层
.close {
  padding: 10px 20px 10px;
  text-align: right;
  box-sizing: border-box;
}
</style>
