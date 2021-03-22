<template>
<<<<<<< HEAD
  <div class='container'>试题预览对话框</div>
</template>

<script>
export default {}
</script>

<style scoped lang='less'></style>
=======
  <div>
    <!-- 题头 -->
    <div class="header_concent">
      <div class="header_text">
        <span>【题型】：</span>
        <span v-if="row.questionType === '1'">简单</span>
        <span v-else-if="row.questionType === '2'">一般</span>
        <span v-else>困难</span>
      </div>
      <div class="header_text">
        <span>【编号】：</span>
        <span>{{row.id}}</span>
      </div>
      <div class="header_text">
        <span>【难度】：</span>
        <span v-if="row.difficulty === '1'">简单</span>
        <span v-else-if="row.difficulty === '2'">一般</span>
        <span v-else>困难</span>
      </div>
      <div class="header_text">
        <span>【标签】：</span>
        <span>{{row.tags}}</span>
      </div>
      <div class="header_text">
        <span>【学科】：</span>
        <span>{{row.subject}}</span>
      </div>
      <div class="header_text">
        <span>【目录】：</span>
        <span>{{row.catalog}}</span>
      </div>
      <div class="header_text">
        <span>【方向】：</span>
        <span>{{row.direction}}</span>
      </div>
    </div>
    <!-- 题干 -->
    <div class="common">
      <div>【题干】：</div>
      <div v-html="row.question" style="color: blue"></div>
      <div>单选题 选项：（以下选中的选项为正确答案）</div>
      <div></div>
    </div>
    <!-- 参考答案 -->
    <div class="common">
      <span>【参考答案】：</span>
    </div>
    <!-- 答案解析 -->
    <div class="common">
      <span>【答案解析】：</span>
      <span>无</span>
    </div>
    <!-- 题目备注 -->
    <div class="remark">
      <span>【题目备注】：</span>
      <span>无</span>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      list: {}
    }
  },
  props: {
    row: {
      type: Object,
      required : true
    }
  },
  created () {
    console.log(this.row)
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.header_concent {
  border-bottom: 1px solid #333;
  overflow: hidden;
  .header_text {
  float: left;
  width: 25%;
  padding: 15px 0;
  }
}

.common {
  border-bottom: 1px solid #333;
  overflow: hidden;
  padding: 15px 0;
}

.remark {
  padding: 15px 0;
}
</style>
>>>>>>> 2e123108ddc771cea7996c882151a66a728b4148
