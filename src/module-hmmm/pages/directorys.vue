<template>
  <div class='container'>
    <el-card class="box-card" style="margin: 10px">
      <!-- 布局 -->
      <el-row>
        <el-col :span="18">
          <!-- 表单 -->
          <el-form :inline="true" ref="directoryForm" :model="reqParams" class="demo-form-inline" size="small" label-width="80px">
            <el-form-item label="目录名称" prop="directoryName">
              <el-input v-model="reqParams.directoryName" placeholder="请输入目录名称"></el-input>
            </el-form-item>
            <el-form-item label="状态" prop="state">
              <el-select v-model="reqParams.state" placeholder="请选择">
                <el-option
                  v-for="item in statusList"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button @click="removeform">清除</el-button>
              <el-button type="primary" @click="obtainList">查询</el-button>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :span="6" style="text-align: right;">
          <el-button @click="returnSubject" type="text" icon="el-icon-back" v-if="$route.query.id">返回学科</el-button>
          <el-button @click="addDirectory" type="success" icon="el-icon-edit" size="small">新增目录</el-button>
        </el-col>
      </el-row>
      <!-- 提示总数量 -->
       <el-alert
        style="margin-bottom: 20px"
        :title="`数据一共 ${total} 条`"
        type="info"
        show-icon
        :closable="false">
      </el-alert>
      <!-- 表格 -->
      <el-table
        :data="tableData"
        style="width: 100%">
        <el-table-column label="序号" width="80px" type="index"></el-table-column>
        <el-table-column label="所属学科" prop="subjectName"></el-table-column>
        <el-table-column label="目录名称" prop="directoryName"></el-table-column>
        <el-table-column label="创建者" prop="username"></el-table-column>
        <el-table-column label="创建日期">
          <template slot-scope="scope">
            {{ scope.row.addDate | parseTimeByString }}
          </template>
        </el-table-column>
        <el-table-column label="面试题数量" prop="totals"></el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <span>已{{ scope.row.state === 1 ? '启用' : '禁用' }}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button @click="disableList(scope.row)" type="text">{{ scope.row.state === 1 ? '禁用' : '启用' }}</el-button>
            <el-button @click="editDirectory(scope.row)" type="text" :disabled="scope.row.state === 1">修改</el-button>
            <el-button @click="deleteList(scope.row.id)" type="text" :disabled="scope.row.state === 1">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
       <el-pagination
          style="float: right; margin: 20px"
          background
          @current-change="changePage"
          @size-change="changeSize"
          :current-page="reqParams.page"
          :page-sizes="[5, 10, 15, 20]"
          :page-size="reqParams.pagesize"
          layout="prev, pager, next, sizes, jumper"
          :total="total">
        </el-pagination>
    </el-card>
    <directory-add ref="Popup" :oneData="oneData" @addSuccess="succeedOpen"></directory-add>
  </div>
</template>

<script>
// 目录列表 api
import { list as directoryList, changeState, remove } from '@/api/hmmm/directorys'
// 状态
import { status } from '@/api/hmmm/constants'
// 弹层组件
import DirectoryAdd from '../components/directorys-add'

export default {
  components: {
    DirectoryAdd
  },
  data () {
    return {
      // 请求参数
      reqParams: {
        // 目录名称
        directoryName: null,
        // 状态 1 开启， 0屏蔽
        state: null,
        // 学科id，需要根据地址动态获取
        subjectID: this.$route.query.id,
        page: 1,
        pagesize: 5
      },
      // 目录数据
      statusList: status,
      // 表格数据
      tableData: [],
      // 总数据
      total: 0,
      // 需要传给子组件的数据
      oneData: {}
    }
  },
  // 侦听器
  watch: {
    '$route.query.id': function () {
      this.reqParams.page = 1
      // 把从地址获取的学科id赋值为学科字段，然后重新渲染，
      // 但是这是如果点击目录，也需要从地址栏获取，但是获取的是undefined，
      // 转变为null，重新渲染，就是获取全部的数据
      this.reqParams.subjectID = this.$route.query.id
      this.obtainList()
    }
  },
  created () {
    this.obtainList()
  },
  methods: {
    // 获取列表
    async obtainList () {
      const { data } = await directoryList(this.reqParams)
      // console.log(data);
      this.tableData = data.items
      this.total = data.counts
    },
    // 清空
    removeform () {
      this.$refs.directoryForm.resetFields()
      this.obtainList()
    },
    // 返回学科
    returnSubject () {
      this.$router.push({ path: '/list', name: 'subjects-list' })
    },
    // 新增目录，调用子组件方法
    addDirectory () {
      this.oneData = {}
      this.$nextTick(() => {
        this.$refs.Popup.open()
      })
    },
    // 修改目录
    editDirectory (row) {
      this.oneData = row
      this.$nextTick(() => {
        this.$refs.Popup.open()
      })
    },
    // 弹窗操作成功
    succeedOpen () {
      // 新增的时候需要跳转到第一页
      if (!this.oneData.id) this.reqParams.page = 1
      this.obtainList()
    },
    // 禁用
    async disableList (row) {
      await changeState({ id: row.id, state: row.state === 1 ? row.state = 0 : row.state = 1 })
      this.$message.success(row.state === 1 ? '启用成功' : '禁用成功')
    },
    // 删除
    deleteList (id) {
      this.$confirm('此操作将永久删除该目录, 是否继续?', '温馨提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        try {
          await remove({ id })
          this.$message.success('删除成功!')
          this.obtainList()
        } catch (e) {
          this.$message.error('删除失败!')
        }
      }).catch(() => {})
    },
    // 分页
    changePage (currentPage) {
      this.reqParams.page = currentPage
      this.obtainList()
    },
    // 分页条数
    changeSize (currentSize) {
      this.reqParams.page = 1
      this.reqParams.pagesize = currentSize
      this.obtainList()
    }
  }
}
</script>

<style scoped lang='scss'></style>