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
      <span class="el-alert__title">数据一共{{msg}}条</span>
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
    <div class="el-pagination is-background" style="margin-top: 20px; text-align: right;" 
    >
      <button type="button" disabled="disabled" class="btn-prev">
        <i class="el-icon el-icon-arrow-left"></i>
      </button>
      <ul class="el-pager">
        <li class="number active">1</li>
      </ul>
      <button type="button" disabled="disabled" class="btn-prev">
        <i class="el-icon el-icon-arrow-right"></i>
      </button>
      <span class="el-pagination__sizes">
        <div class="el-select el-select--mini">
          <div class="el-input el-input--mini el-input--suffix">
            <input type="text" readonly="readonly" autocomplete="off" placeholder="请选择" class="el-input__inner">
            <span class="el-input__suffix">
              <span class="el-input__suffix-inner">
                <i class="el-select__caret el-input__icon el-icon-arrow-up"></i>
              </span>
            </span>
          </div>
        </div>
      </span>
      <span class="el-pagination__jump">前往
        <div class="el-input el-input--medium el-pagination__editor is-in-pagination">
          <input type="number" autocomplete="off" min="1" max="2" class="el-input__inner">
        </div>页
      </span>
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
      msg: '',
      dataList: []
    }
  },
  created (){
    this.getSubject ()
  },
  methods:{
    //分页
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getDirectorysList()
    },
    handleCurrentChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getDirectorysList()
    },
    //获取列表
    async getSubject () {
      const { data } = await list () 
      console.log(data)
      this.dataList = data.items
      console.log(this.dataList)
      this.msg = data.counts
      console.log(this.msg)
    }
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
</style>
