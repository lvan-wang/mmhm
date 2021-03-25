<template>
  <div>
    <!-- 题头 -->
    <div class="header_concent">
      <div class="header_text">
        <span>【题型】：</span>
        <span v-if="row.questionType === '1'">单选题</span>
        <span v-else-if="row.questionType === '2'">多选题</span>
        <span v-else>简答题</span>
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
        <span>{{row.subjectName}}</span>
        <span>{{row.subject}}</span>
      </div>
      <div class="header_text">
        <span>【目录】：</span>
        <span>{{row.catalog}}</span>
        <span>{{row.directoryName}}</span>
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
      <!-- 题型 -->
      <div v-if="row.questionType === '1'">单选题 选项：（以下选中的选项为正确答案）</div>
      <div v-else-if="row.questionType === '2'">多选题 选项：（以下选中的选项为正确答案）</div>
      <div v-else>简答题 (正确答案如下)</div>
      <!-- 答案列表 -->
      <div>
        <!-- 单选题的情况 -->
        <div v-if="row.questionType === '1'">
          <div v-for="item, index in options" :key="index" class="questionType_checkbox">
            <el-checkbox-group :value="checkboxItem">
              <el-checkbox :label="item.title" ></el-checkbox>
              <img v-if="item.img" class="timutupian" :src="item.img">
            </el-checkbox-group>
          </div>
          
        </div>
        <!-- 多选题的情况 -->
        <div v-else-if="row.questionType === '2'">
          <div v-for="item, index in options" :key="index" class="questionType_checkbox">
            <el-checkbox-group :value="checkboxItem">
              <el-checkbox :label="item.title" ></el-checkbox>
            </el-checkbox-group>
          </div>
        </div>
        <!-- 简答题的情况 -->
        <div v-else>3</div>
      </div>
    </div>
    <!-- 参考答案 -->
    <div class="common">
      <span>【参考答案】：</span>
      <el-button size="mini" type="danger" @click="clickVideoBtn">视频答案预览</el-button>
      <div v-show="videoShow" class="video">
        <video :src="row.videoURL"  controls="controls" style="width: 50%">
        您的浏览器不支持 video 标签。
        </video>
      </div>
    </div>
    <!-- 答案解析 -->
    <div class="common daanjiexibox">
      <span >【答案解析】：</span>
      <span class="daanjiexi" v-html="row.answer">{{row.answer}}</span>
    </div>
    <!-- 题目备注 -->
    <div class="remark">
      <span>【题目备注】：</span>
      <span>{{row.remarks}}</span>
    </div>
  </div>
</template>

<script>
import { detail } from '@/api/hmmm/questions'
export default {
  data () {
    return {
      list: {},
      options: [], // 答案的数据列表
      checkboxItem: [], // 默认被选中的答案
      videoShow: false // 视频的显示和影藏
    }
  },
  props: {
    row: {
      type: Object,
      required : true
    }
  },
  created () {
    // console.log(this.row)
    this.getAnswer()
  },
  methods: {
    async getAnswer () {
      try {
        const { data } = await detail({id: this.row.id})
        // console.log(data)
        this.options = data.options
        const checkboxItem = this.options.filter(item => {
          return item.isRight === 1
        })
        const newCheckboxItem = checkboxItem.map(function (arr) {
          return arr.title
        })
        // console.log(newCheckboxItem)
        this.checkboxItem = newCheckboxItem
      } catch (err) {
        this.$message.error('获取数据失败')
      }
    },
    // 播放视频的按钮
    clickVideoBtn () {
      if (this.row.videoURL) {
        this.videoShow = true
      } else {
        return this.$message.error('该题目没有视频答案')
      }
    }
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

.video {
  position: relative;
  margin: 15px 20px;
  width: 100%;
  img {
  display: none;
  position: absolute;
  top: 48%;
  left: 25%;
  margin-left: 0!important;
  transform: translate(-50%, -50%);
  width: 100px;
  height: 100px;
	margin-left: 40%;
  opacity: .9;
  z-index: 2;
  }
  
}
.video:hover img {
  display: block;
}
.questionType_checkbox {
  padding: 5px 0;
}
.daanjiexibox {
  position: relative;
}
.daanjiexi {
  position: absolute;
  bottom: 0px;
}
.timutupian {
  margin: 0 20px;
  vertical-align: top;
  width: 100px;
  height: 100px;
}
</style>
