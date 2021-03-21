<template class = "tag-container">
  <div class="container">
    <!-- 卡片区域 -->
    <el-card class="cardTop">
      <!-- 标签头部 -->
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="标签名称">
          <el-input v-model="formInline.user"></el-input>
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model="formInline.region" placeholder="请选择">
            <el-option label="区域一" value="shanghai"></el-option>
            <el-option label="区域二" value="beijing"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button @click="onDeleate">清除</el-button>
          <el-button type="primary" @click="onSubmit">搜索</el-button>
          <el-button type="success" class="successButton">成功按钮</el-button>
        </el-form-item>
      </el-form>
      <!-- 消息提示框 -->
      <el-alert title="数据一共 11 条" type="info" show-icon> </el-alert>
      <!-- 标签正文 -->
      <el-table :data="tableData" style="width: 100%" class="label">
        <el-table-column
          prop="serialNumber"
          label="序号"
          width="180"
        ></el-table-column>
        <el-table-column
          prop="subject"
          label="学科"
          width="180"
        ></el-table-column>
        <el-table-column prop="labelName" label="目录名称"></el-table-column>
        <el-table-column prop="name" label="创建者"></el-table-column>
        <el-table-column prop="date" label="创建日期"></el-table-column>
        <el-table-column prop="questions" label="面试题数量"></el-table-column>
        <el-table-column prop="state" label="状态"></el-table-column>
        <el-table-column prop="operation" label="操作"></el-table-column>
      </el-table>
      <div class="block">
        <el-pagination
          class="block-right"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage4"
          :page-sizes="[5, 10, 20, 50]"
          :page-size="100"
          layout="total, sizes, prev, pager, next, jumper"
        >
        </el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formInline: {
        user: "",
        region: "",
      },
      tableData: [
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
        {
          serialNumber: "1",
          subject: "大数据",
          labelName: "数据存储",
          name: "超级管理员",
          date: "2016-05-02",
          questions: "0",
          state: "已启用",
          operation: "禁用",
        },
      ],
      currentPage1: 5,
      currentPage2: 5,
      currentPage3: 5,
      currentPage4: 4,
    };
  },
  methods: {
    onSubmit() {
      console.log("submit!");
    },
    onDeleate() {
      console.log("onDeleate");
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
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
  position: fixed;
  right: 40px;
}

.label {
  margin-top: 16px;
}
.block-right{
  float: right;
}
</style>

