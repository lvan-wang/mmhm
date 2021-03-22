<template>
<!-- 头部 -->
  <div class='container'><el-card>
      <div class="xuekemingchengwaikuang">
        <span class="xuekemingcheng">学科名称</span>
    <input type="text" autocomplete="off" class="el-input_inner">
    <button type="button" class="el-button el-button--default el-button--small"><span>清除</span></button>
    <button type="button" class="el-button el-button--primary el-button--small"><span>搜索</span></button>
    <button type="button" class="xinzengxueke el-button el-button--success el-button--small"><i class="el-icon-edit"></i><span>新增学科</span></button>
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
      type="index"
        label="序号"
        width="180">
      </el-table-column>
      <el-table-column
        prop="subjectName"
        label="学科名称"
        width="180">
      </el-table-column>
      <el-table-column
        prop="username"
        label="创建者">
      </el-table-column>
      <el-table-column
        prop="addDate"
        label="创建日期"
        width="200">
      </el-table-column>
      <el-table-column
        prop="isFrontDisplay"
        label="前台是否显示">
      </el-table-column>
      <el-table-column
        prop="twoLevelDirectory"
        label="二级目录">
      </el-table-column>
      <el-table-column
        prop="tags"
        label="标签">
      </el-table-column>
      <el-table-column
        prop="totals"
        label="题目数量">
      </el-table-column>
      <el-table-column
        label="操作"
        width="300">
        <a href="javascript:;" class="caozuo_a">学科分类</a>
        <a href="javascript:;" class="caozuo_a">学科标签</a>
        <a href="javascript:;" class="caozuo_a">修改</a>
        <a href="javascript:;" class="caozuo_a">删除</a>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <div class="fenyeyouyi">
     <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="queryInfo.page" :page-sizes="[5, 10, 20, 50]" :page-size="queryInfo.pagesize" layout="prev, pager, next, total, sizes, jumper" :total="total">
      </el-pagination>
      </div>
  </template>
    </el-card>
  </div>
</template>

<script>
import { list } from '@/api/hmmm/subjects'
export default {
  data (){
    return {
      // 获取学科列表的参数对象
      queryInfo: {
        query: '',
        page: 1,
        pagesize: 10,
      },
      //数据条数
      total: null,
      //列表
      dataList: [],
    }
  },
  created (){
    this.getSubjectList ()
  },
  methods:{
    //获取列表
    async getSubjectList () {
      const { data } = await list ({page:this.queryInfo.page,pagesize:this.queryInfo.pagesize}) 
      console.log(data)
      this.dataList = data.items
      this.total = data.counts
    },
    //分页
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getSubjectList()
    },
    handleCurrentChange(newPage) {
      this.queryInfo.page = newPage
      this.getSubjectList()
    },
  }
}
</script>

<style rel="stylesheet/scss" lang="scss">
.xuekemingchengwaikuang {
  margin-bottom: 20px;
}
.xuekemingcheng {
  font-size: 14px;
  font-weight: 700;
  margin-right: 12px;
  color: #606266;
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
.el-button {
  margin: 0 0 0 15px;
}
.el-alert__title {
  padding: 0 8px;
}
.xinzengxueke {
  position: absolute;
  right: 42px;
}
.caozuo_a{
  color: blue;
  margin-right: 15px;
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
