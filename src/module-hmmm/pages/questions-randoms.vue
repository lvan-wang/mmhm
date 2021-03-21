<template>
<!-- 头部 -->
  <div class='container'><el-card>
      <div class="guanjianziwaikuang">
        <span class="guanjianzi">关键字</span>
    <input type="text" autocomplete="off" class="el-input_inner" placeholder="根据编号搜索">
    <div class="nuodaoyoubian">
      <button type="button" class="el-button el-button--default el-button--small"><span>清除</span></button>
    <button type="button" class="el-button el-button--primary el-button--small"><span>搜索</span></button>
    </div>
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
      <button type="button" class=" shanchulajitong el-button el-button--danger el-button--small is-plain is-circle" title="删除" style="margin:0">
        <i class="el-icon-delete"></i>
      </button>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <div class="el-pagination is-background" style="margin-top: 20px; text-align: right;">
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
import { randoms } from '@/api/hmmm/questions'
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
    async getSubject () {
      const { data } = await randoms () 
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
</style>
