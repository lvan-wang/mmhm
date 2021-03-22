<template>
<!-- 头部 -->
<!-- 头部 -->
  <div class='container'><el-card>
      <div class="guanjianziwaikuang">
        <span class="guanjianzi">关键字</span>
        <el-input @clear="getQuestionsList" v-model="queryInfo.keyword" size="small" style="height: 20px; width:200px"  placeholder="根据编号搜索"></el-input>
        <div class="nuodaoyoubian">
          <el-button size="small" class="colInput" plain @click="clear">清除</el-button>
          <el-button slot="append"  size="small" class="colInput" type="primary" @click="getQuestionsList">搜索</el-button>
        </div>
      </div>
    <div role="alert" class="el-alert alert el-alert--info is-light" style="margin-bottom: 15px;">
      <i class="el-alert__icon el-icon-info"></i>
      <span class="el-alert__title">数据一共{{total}}条</span>
      </div>
        <template>
          <!-- 表格 -->
    <el-table
      :data="dataList"
      style="width: 100%">
      <el-table-column
        prop="id"
        label="编号"
        width="200">
      </el-table-column>
      <el-table-column
        prop="questionType"
        label="题型"
        width="180">
      </el-table-column>
      <el-table-column
        label="题目编号">
        <template slot-scope="scope">
          <span v-for="item, index in scope.row.questionIDs" :key="index"><a href="javascript:;" class="timubianhao">{{item.number}}<br></a></span>
        </template>
      </el-table-column>
      <el-table-column
        prop="addTime"
        label="录入时间">
      </el-table-column>
      <el-table-column
        prop="totalSeconds"
        label="答题时间(s)">
      </el-table-column>
      <el-table-column
        prop="accuracyRate"
        label="正确率(%)">
      </el-table-column>
      <el-table-column
        prop="userName"
        label="录入人">
      </el-table-column>
      <el-table-column
        label="操作">
        <template slot-scope="scope">
      <button @click="delQuestions(scope.row)" type="button" class=" shanchulajitong el-button el-button--danger el-button--small is-plain is-circle" title="删除" style="margin:0">
        <i class="el-icon-delete"></i>
      </button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <div class="fenyeyouyi">
     <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="queryInfo.page" :page-sizes="[20, 30, 50, 100]" :page-size="queryInfo.pagesize" layout="prev, pager, next, total, sizes, jumper" :total="total">
      </el-pagination>
      </div>
  </template>
    </el-card>
  </div>
</template>

<script>
import { randoms } from '@/api/hmmm/questions'
import { removeRandoms } from '@/api/hmmm/questions'
export default {
  data (){
    return {
      // 获取学科列表的参数对象
      queryInfo: {
        keyword: '',
        page: 1,
        pagesize: 20,
      },
      //数据条数
      total: null,
      //列表
      dataList: [],
    }
  },
  created (){
    this.getQuestionsList()
  },
  methods:{
    //获取列表-搜索列表
    async getQuestionsList () {
      const { data } = await randoms ({keyword:this.queryInfo.keyword,page:this.queryInfo.page,pagesize:this.queryInfo.pagesize}) 
      console.log(data)
      this.dataList = data.items
      this.total = data.counts
    },
    //分页
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getQuestionsList ()
    },
    handleCurrentChange(newPage) {
      this.queryInfo.page = newPage
      this.getQuestionsList ()
    },
    //清除
    clear () {
      this.queryInfo = {
        subjectName: '',
      }
    },
    //删除组题
    async delQuestions (id) {
    await this.$confirm('此操作将永久删除该学科, 是否继续?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning'
    })
    await removeRandoms(id)
    this.$message.success('删除成功')
    this.getQuestionsList()
    },
  }
}
</script>

<style rel="stylesheet/scss" lang="scss">
.guanjianziwaikuang {
  margin-bottom: 20px;
}
.guanjianzi {
  font-size: 14px;
  font-weight: 700;
  margin-right: 12px;
  color: #606266;
}
.nuodaoyoubian {
  position: absolute;
  right: 42px;
  top: 42px;
}
.el-input_inner {
  border-radius: 4px;
  border: 1px solid #dcdfe6;
  color: #606266;
  display: inline-block;
  font-size: inherit;
  height: 32px;
  line-height: 32px;
  outline: 0;
  padding: 0 15px;
}
.el-input_inner::placeholder{
  font-size: 14px;
  color:#ccc;
}
.el-button {
  margin: 0 0 0 15px;
}
.el-alert__title {
  padding: 0 8px;
}
.timubianhao {
  color: blue;
}
.fenyeyouyi {
  margin-top: 20px;
  height: 28px;
}
.el-pagination {
  position: absolute;
  right: 42px;
}
</style>
