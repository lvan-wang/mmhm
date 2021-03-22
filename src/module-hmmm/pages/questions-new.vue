<template>
  <div class='container'>
    <!-- 卡片 -->
    <el-card>
      <!-- 头部 -->
        <div class="card_header">试题录入</div>
      <!-- 内容 500 -->
      <el-form ref="form" :model="form" label-width="80px" :rules="rules" class="card_body">
        <!-- 学科 -->
        <el-form-item label="学科" prop="region">
          <el-select v-model="form.region" placeholder="请选择活动区域" class="select_content">
            <el-option label="区域一" value="shanghai"></el-option>
            <el-option label="区域二" value="beijing"></el-option>
          </el-select>
        </el-form-item>
        <!-- 目录 -->
        <el-form-item label="目录" prop="region">
          <el-select v-model="form.region" placeholder="请选择活动区域" class="select_content">
            <el-option label="区域一" value="shanghai"></el-option>
            <el-option label="区域二" value="beijing"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
    return {
      form: {
        discipline: '' // 学科的相关数据
      },
      // 验证规则
      rules: {
        region: [
          { required: true, message: '请选择活动区域', trigger: 'change' }
        ]
      }
    }
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
    width: 500px;
  }
}
</style>
