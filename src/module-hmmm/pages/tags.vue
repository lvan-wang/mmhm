<template>
  <div class='tags-container'>
    <div class="app-container">
      <el-card shadow="never">
        <!-- 搜索 -->
        <el-form :inline="true" :model="requestParameters"
        ref="requestParameters" class="demo-form-inline">
          <el-form-item label="标签名称：" prop="tags">
            <el-input v-model="requestParameters.tags"></el-input>
          </el-form-item>
          <el-form-item label="状态：" prop="state">
            <el-select v-model="requestParameters.state" placeholder="请选择">
              <el-option
              v-for="item in status"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button
            @click="resetForm"
            >清除</el-button>
            <el-button
            type="primary"
            @click="handleFilter"
            >搜索</el-button>
          </el-form-item>
          <el-button
              class="filter-item fr"
              size="small"
              style="margin-left: 10px;"
              type="success"
              icon="el-icon-edit"
              @click="addEditTags('add')"
            >新增标签</el-button>
        </el-form>
        <!-- /搜索 -->

      <!-- 数据记录 -->
      <el-alert :title="'数据一共'+total+'条'" type="info" show-icon :closable="false">
      </el-alert>
      <!-- /数据记录 -->

      <!-- 数据列表 -->
      <el-table
      :data="dataList"
      fit
      style="width: 100%">
        <el-table-column type="index" label="序号">
        </el-table-column>
        <el-table-column prop="subjectName" label="所属学科">
        </el-table-column>
        <el-table-column prop="tagName" label="标签名称">
        </el-table-column>
        <el-table-column prop="username" label="创建者">
        </el-table-column>
        <el-table-column prop="addDate" label="创建日期">
          <template slot-scope="scope">
              <span>{{scope.row.addDate | parseTimeByString }}</span>
            </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
              <span v-if="scope.row.state==1">已启用</span>
              <span v-else>已禁用</span>
            </template>
        </el-table-column>
        <el-table-column label="操作" width="280">
          <template slot-scope="scope">
            <el-button size="mini" type="warning" @click="handleStatus(scope.row)">
               <span v-if="scope.row.state==0">启用</span>
                <span v-else>禁用</span>
            </el-button>

            <el-button size="mini" type="primary"
            @click="addEditTags(scope.row.id)"
            v-bind:disabled="scope.row.state === 1"
            >编辑</el-button>
            <el-button size="mini" type="danger"
            @click="removeTags(scope.row.id)"
            v-bind:disabled="scope.row.state === 1"
            >删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- /数据列表 -->

      <!-- 分页 -->
      <div class="pagination">
        <el-pagination
      background
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="requestParameters.pagenum"
      :page-sizes="[5, 10, 15, 20]"
      :page-size="requestParameters.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
      </el-pagination>
      </div>
      <!-- /分页 -->
      <!-- 新增标签弹层 -->
        <Dialog ref="editTags" :titleInfo="titleInfo" :formBase="formData" v-on:newDataes="loadTagsList"></Dialog>
      </el-card>
    </div>
  </div>
</template>

<script>
import { status } from '@/api/hmmm/constants'
import { list, remove, changeState, detail } from '@/api/hmmm/tags'
import Dialog from './../components/tags-add'

export default {
  name: 'TagsList',
  components: {
    Dialog
  },
  data () {
    return {
      requestParameters: {
        tags: '',
        // 当前的页数
        pagenum: 1,
        // 当前每页显示多少条数据
        pagesize: 5
      },
      // 表格数据列表
      dataList: [],
      // 数据总条数
      total: null,
      titleInfo: {
        pageTitle: '标签', // 页面标题
        text: '' // 新增、编辑文本
      },
      formData: {
        subjectName: '',
        tagName: ''
      }
    }
  },
  computed: {
    status () {
      return status
    }
  },
  created () {
    this.loadTagsList()
  },
  mounted () {},
  methods: {
    // 监听页容量pagesize变化
    handleSizeChange(newSize) {
      console.log(`每页 ${newSize} 条`)
      this.requestParameters.pagesize = newSize
      this.loadTagsList()
    },
    // 监听当前页码值变化
    handleCurrentChange(newPage) {
      console.log(`当前页: ${newPage}`)
      this.requestParameters.pagenum = newPage
      this.loadTagsList()
    },
    // 加载标签列表
    async loadTagsList () {
      const { data } = await list({
        page: this.requestParameters.pagenum,
        pagesize: this.requestParameters.pagesize,
        tagName: this.requestParameters.tags,
        state: this.requestParameters.state
      })
      console.log(data)
      this.dataList = data.items
      this.total = data.counts
    },
    // 重置搜索表单区
    resetForm () {
      this.$refs.requestParameters.resetFields()
    },
    // 查询搜索对应标签
    handleFilter () {
      this.loadTagsList()
    },
    // 启用、禁用
    handleStatus (val) {
      console.log(val)
      var status = ''
      if (val.state) {
        val.state = 0
        status = '禁用'
      } else {
        val.state = 1
        status = '启用'
      }
      console.log(status)
      var data = {
        id: val.id,
        state: val.state
      }
      this.$confirm('已成功' + status + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async (val) => {
          await changeState(data)
            .then(response => {
              this.$message.success('已成功' + status + '!')
              console.log('成功,已执行')
              console.log(response)
              this.loadTagsList(this.requestParameters)
            })
            .catch(response => {
              this.$message.error(status + '失败!')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    },
    // 删除标签
    async removeTags (id) {
      console.log(id)
      // 弹框询问用户是否删除数据
      const confirmResult = await this.$confirm(
        '此操作将永久删除该用户, 是否继续?',
        '提示',
        {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }
      ).catch(err => err)
      // 如果用户确认删除，则返回值为字符串 confirm
      // 如果用户取消了删除，则返回值为字符串 cancel
      console.log(confirmResult)
      if (confirmResult !== 'confirm') {
        return this.$message.info('已取消删除')
      }
      
      await remove({id})
      this.$message.success('删除用户成功！')
      this.loadTagsList()
    },
    // 新增、编辑标签
    query () {
        this.formData = {}
    },
    addEditTags (id) {
      this.query()
      this.$refs.editTags.dialogFormV()
      if (id === 'add') {
        this.titleInfo.text = '新增' // 给弹层传参
      } else {
        this.titleInfo.text = '编辑' // 给弹层传参
        this.hanldeEditForm(id)
      }
    },
    // 表单详情数据加载
    async hanldeEditForm (objeditId) {
      this.formData.id = objeditId
      const { data: res } = await detail({ id: objeditId })
      // 获取详情 给formData
      this.formData = res
      console.log(this.formData)
      this.formData.subjectName = res.subjectID
    }
  }
}
</script>

<style scoped lang='less'>
.tags-container {
  .el-table {
    padding-top: 20px;
  }
  .pagination {
    padding-top: 20px;
    height:75px;
    float: right;
  }
}
</style>
