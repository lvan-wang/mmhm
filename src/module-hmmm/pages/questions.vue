<template>
  <div class='container'>题组管理
    <el-card>
      <!-- 头部导语和按钮 -->
      <div class="card_header">
        <span class="header_text">说明：目前支持学科和关键字条件筛选</span>
        <el-button type="success" icon="el-icon-edit" size="small">新增试题</el-button>
      </div>
      <!-- 头部选择区域 -->
      <div class="header_select">
        <!-- 第一个 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="学科">
              <el-select v-model="form.region" placeholder="请选择" style="width: 100%">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <!-- 第二个 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="二级目录">
              <el-select v-model="form.region" placeholder="请选择" style="width: 100%">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <!-- 三 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="标签">
              <el-select v-model="form.region" placeholder="请选择" style="width: 100%">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <!-- 四 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="关键字">
              <el-input v-model="form.name" placeholder="根基题干搜索"></el-input>
            </el-form-item>
          </el-form>
        </div>
        <!-- 五 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="试题类型">
              <el-select v-model="form.region" placeholder="请选择" style="width: 100%">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <!-- 六 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="难度">
              <el-select v-model="form.region" placeholder="请选择" style="width: 100%">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <!-- 七 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="方向">
              <el-select v-model="form.region" placeholder="请选择" style="width: 100%">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <!-- 八 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="录入人">
              <el-select v-model="form.region" placeholder="请选择" style="width: 100%">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <!-- 九 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="题目备注">
              <el-input v-model="form.name"></el-input>
            </el-form-item>
          </el-form>
        </div>
        <!-- 十 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="企业简称">
              <el-input v-model="form.name"></el-input>
            </el-form-item>
          </el-form>
        </div>
        <!-- 十一 -->
        <div class="header_col">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="城市">
              <el-cascader :options="cityData" style="width:100%"></el-cascader>
            </el-form-item>
          </el-form>
        </div>
        <!-- 十二 两个按钮 -->
        <div class="header_col">
          <el-button class="col_button" size="mini" type="primary">搜索</el-button>
          <el-button class="col_button" size="mini">清除</el-button>
        </div>
      </div>
      <!-- 提示消息 -->
      <el-alert
        title="数据一共 7 条"
        type="info"
        show-icon
        :closable="false">
      </el-alert>
      <!-- 数据详细清况 -->
      <div class="body_list">
        <!-- TODO: :data="dataList" 没有绑定数据u -->
        <el-table
          stripe
          style="width: 100%">
          <el-table-column label="试题编号" prop="order_price" width="100"></el-table-column>
          <el-table-column label="学科" prop="order_price"></el-table-column>
          <el-table-column label="目录" prop="order_price"></el-table-column>
          <el-table-column label="题型" prop="order_price"></el-table-column>
          <el-table-column label="题干" prop="order_price" width="240"></el-table-column>
          <el-table-column label="录入时间" prop="order_price"></el-table-column>
          <el-table-column label="难度" prop="order_price"></el-table-column>
          <el-table-column label="录入人" prop="order_price"></el-table-column>
          <el-table-column label="操作" prop="order_price" width="180"></el-table-column>
        </el-table>
        <!-- 分页按钮 -->
        <div class="paging_btn">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage4"
            :page-sizes="[100, 200, 300, 400]"
            :page-size="100"
            layout=" prev, pager, next, sizes, jumper"
            :total="400">
          </el-pagination>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
import cityData from '../citydata'
export default {
  data () {
    return {
      form: {
        region: '',
        name: ''
      },
      // 省市的联动
      cityData: cityData,
      currentPage4: 4 
    }
  },
  methods: {
    // 每页容量变化刷新页面
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    // 当前页码变化更新页面
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    }
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

  .header_col {
    float: left;
    width: 25%;
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
    .el-pagination {
      padding: 20px 10px;
    }
  }
}
</style>
