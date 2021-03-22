<template class = "tag-container">
  <div class="container">
    <!-- 卡片区域 -->
    <el-card class="cardTop">
      <!-- 搜索 -->
      <el-form
        :inline="true"
        :model="queryInfo"
        class="demo-form-inline"
      >
        <el-form-item label="标签名称">
          <el-input
            v-model="queryInfo.directoryName"
            @keyup.enter="onSubmit"
          ></el-input>
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model="queryInfo.state" placeholder="请选择">
            <el-option
              v-for="item in directorysTypeList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button @click="onDeleate">清除</el-button>
          <el-button type="primary" @click="onSubmit">搜索</el-button>
        </el-form-item>
        <el-button type="success" class="successButton el-icon-edit"
          >新增目录</el-button
        >
      </el-form>
      <!-- 消息提示框 -->
      <el-alert :title="'数据一共' + total + '条'" type="info" show-icon>
      </el-alert>
      <!-- 标签正文 -->
      <el-table :data="directorysList" style="width: 100%" class="label">
        <el-table-column
          type="index"
          prop="id"
          label="序号"
          width="80"
        ></el-table-column>
        <el-table-column
          prop="subjectName"
          label="所属学科"
          width="180"
        ></el-table-column>
        <el-table-column
          prop="directoryName"
          label="目录名称"
        ></el-table-column>
        <el-table-column prop="username" label="创建者"></el-table-column>
        <el-table-column prop="addDate" label="创建日期"></el-table-column>
        <el-table-column prop="totals" label="面试题数量"></el-table-column>
        <el-table-column prop="state" label="状态">
          <template slot-scope="scope">
            <span v-if="scope.row.state == 1">已启用</span>
            <span v-else>已禁用</span>
          </template>
        </el-table-column>
        <el-table-column prop="operation" label="操作" class="operation">
          <button
            type="button"
            class="el-button el-button--text el-button--medium"
            @click="handleDisable"
          >
            禁用
          </button>
          <button
            type="disabled"
            class="el-button el-button--text el-button--medium"
          >
            修改
          </button>
          <button
            type="disabled"
            class="el-button el-button--text el-button--medium"
          >
            删除
          </button>
        </el-table-column>
      </el-table>
      <!-- 分页区域 -->
      <el-pagination
        background
        class="block-right"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :page-size="queryInfo.pagesize"
        :current-page="queryInfo.pagenum"
        :page-sizes="[5, 10, 20, 50]"
        layout="prev, pager, next, sizes, jumper"
        :total="total"
      >
      </el-pagination>
    </el-card>
  </div>
</template>

<script>
import { list } from "@/api/hmmm/directorys";
export default {
  data() {
    return {
      // 获取学科目录的参数对象
      queryInfo: {
        query: "",
        page: 1,
        pagesize: 10,
        directoryName: "",
      },

      // 目录列表
      directorysList: [],
      // 总数据条数
      total: 0,

      directorysTypeList: [{
        value: 0,
        table: '禁用'
      },{
        value: 1,
        table: '启用'
      }]
    }
    
  },
  created() {
    this.getDirectorysList();
  },
  // 加载标签列表
  methods: {
    async getDirectorysList() {
      const params = {
        pagesize: this.queryInfo.pagesize,
        page: this.queryInfo.pagenum,
        directoryName: this.queryInfo.directoryName,
        state: this.queryInfo.state,
      };
      const { data: res } = await list(params);
      // if (res.status !== 200) {
      //   console.log(res.status)
      //   return this.$message.error ('获取学科目录失败')
      // }
      // this.$message.success('获取学科目录成功')
      console.log(res);
      this.directorysList = res.items;
      this.total = res.counts;
      // this.queryInfo.state = res.
    },
    // 操作 禁用按钮点击
    handleDisable() {},

    handleSizeChange(newSize) {
      console.log(newSize);
      this.queryInfo.pagesize = newSize;
      this.getDirectorysList();
    },
    handleCurrentChange(newPage) {
      this.queryInfo.pagenum = newPage;
      this.getDirectorysList();
    },
    // 点击搜索按钮
    async onSubmit() {
      console.log("00000000000000");
      // this.formInline.page = 1
      const { data } = await list(this.queryInfo);
      console.log(data);
      this.directorysList = data.items;
      this.total = data.counts;
    },
    // 点击清除按钮
    onDeleate() {
      console.log("onDeleate");
      this.queryInfo.directoryName = ''
      // this.$refs.formInline.reseFields()
    },
  },
};
</script>

<style rel="stylesheet/scss" lang="scss">
.container {
  padding: 0;
}
.cardTop {
  margin-top: 10px;
  margin-left: 10px;
  margin-right: 10px;
}

.successButton {
  position: absolute;
  right: 40px;
}

.label {
  margin-top: 16px;
}
.block-right {
  float: right;
}
</style>

