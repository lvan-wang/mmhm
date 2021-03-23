<template>
<!-- 头部 -->
  <div class='container'><el-card>
      <div class="xuekemingchengwaikuang">
        <span class="xuekemingcheng">学科名称</span>
        <el-input @clear="getSubjectsList" v-model="queryInfo.subjectName"  size="small" style="height: 20px; width:200px"></el-input>
          <el-button size="small" class="colInput" plain @click="clear">清除</el-button>
          <el-button slot="append"  size="small" class="colInput" type="primary" @click="getSubjectsList">搜索</el-button>
    <button   @click="addDialogVisible = true" type="button" class="xinzengxueke el-button el-button--success el-button--small "><i class="el-icon-edit"></i><span>新增学科</span></button>
      </div>
    <div role="alert" class="el-alert alert el-alert--info is-light" style="margin-bottom: 15px;">
      <i class="el-alert__icon el-icon-info"></i>
      <span class="el-alert__title">数据一共{{total}}条</span>
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
        <template slot-scope="scope">
            <el-button type="text" @click="$router.push(`directorys?id=${scope.row.id}&name=${encodeURIComponent(scope.row.subjectName)}`)">学科分类</el-button>
            <el-button type="text" @click="$router.push(`tags?id=${scope.row.id}&name=${encodeURIComponent(scope.row.subjectName)}`)">学科标签</el-button>
            <el-button type="text" @click="editSubList(scope.row)">修改</el-button>
            <el-button type="text" @click="delSubject(scope.row)">删除</el-button>
          </template>
      </el-table-column>
    </el-table>
    <!-- 添加的弹框 -->
    <el-dialog title="新增学科" :visible.sync="addDialogVisible" width="20%" @close="resetSub">
      <!-- 主体区域 -->
      <el-row>
        <el-col>
          <el-form :model="addSubjectsFrom" :rules="addSubjectsFromRules" ref="addSubjectsFromRef" label-width="80px" class="demo-ruleForm">
            <el-form-item label="学科名称" prop="subjectName">
              <el-input v-model="addSubjectsFrom.subjectName" placeholder="请输入学科名称"></el-input>
            </el-form-item>
          </el-form>
        </el-col>
        <span class="subspan">是否显示</span>
        <el-switch v-model="SubSwitchValue" active-color="#13ce66" inactive-color="#ff4949"> </el-switch>
      </el-row>
      <!-- 按钮 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addSubFrom">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 修改的弹框 -->
    <el-dialog title="修改学科" :visible.sync="editDialogVisible" width="20%" @close="editResetSub">
      <!-- 主体区域 -->
      <el-row>
        <el-col>
          <el-form :model="editSubjectsFrom" :rules="editSubjectsFromRules" ref="editSubjectsFromRef" label-width="80px" class="demo-ruleForm">
            <el-form-item label="学科名称" prop="subjectName">
              <el-input v-model="editSubjectsFrom.subjectName"></el-input>
            </el-form-item>
          </el-form>
        </el-col>
        <span class="subspan">是否显示</span>
        <el-switch v-model="editSubjectsFrom.isFrontDisplay" active-color="#13ce66" inactive-color="#ff4949"> </el-switch>
      </el-row>
      <!-- 按钮 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editSubFrom">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 分页 -->
    <div class="fenyeyouyi">
     <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="queryInfo.page" :page-sizes="[5, 10, 20, 50]" :page-size="queryInfo.pagesize" layout="prev, pager, next, total, sizes, jumper" :total="total">
      </el-pagination>
      </div>
  </template>
    </el-card>
  </div>
</template>

<script>
import { remove } from '@/api/hmmm/subjects'
import { add } from '@/api/hmmm/subjects'
import { list } from '@/api/hmmm/subjects'
import { update } from '@/api/hmmm/subjects'
export default {
   name: 'Subjects',
  data (){
    return {
      addDialogVisible : false,
      // 获取学科列表的参数对象
      queryInfo: {
        subjectName: '',
        page: 1,
        pagesize: 10,
      },
      //数据条数
      total: null,
      //列表
      dataList: [],
      // 添加学科是否显示的开关
      SubSwitchValue: true,
      // 添加的验证
      addSubjectsFromRules: {
        subjectName: [
          { required: true, message: '请输入学科名称', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 1 到 10 个字符', trigger: 'blur' }
        ]
      },
      // 添加的输入框数据
      addSubjectsFrom: {
        subjectName: ''
      },
       // 修改的id
      userId: '',
      // 修改的默认表单值
      editSubjectsFrom: {
        subjectName: '',
        isFrontDisplay: ''
      },
      // 修改的验证
      editSubjectsFromRules: {
        subjectName: [
          { required: true, message: '请输入学科名称', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 1 到 10 个字符', trigger: 'blur' }
        ]
      },
      // 修改学科的弹框显示
      editDialogVisible: false,
    }
  },
  created (){
    this.getSubjectsList ()
  },
  computed: {
    isFrontDisplay () {
      if (this.SubSwitchValue) {
        return 1
      } else {
        return 0
      }
    }
  },
  methods:{
    //获取列表-搜索列表
    async getSubjectsList () {
      try {
      const { data } = await list ({subjectName:this.queryInfo.subjectName,page:this.queryInfo.page,pagesize:this.queryInfo.pagesize}) 
      console.log(data)
      this.dataList = data.items
      this.total = data.counts
       } catch (err) {
         this.$message.error('获取数据失败')
       }
    },
    //清除
    clear () {
      this.queryInfo = {
        subjectName: '',
      }
    },
    //分页
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getSubjectsList()
    },
    handleCurrentChange(newPage) {
      this.queryInfo.page = newPage
      this.getSubjectsList()
    },
     // 添加学科
    async addSubFrom () {
      try {
        await add ({
          subjectName: this.addSubjectsFrom.subjectName,
          isFrontDisplay: this.isFrontDisplay
        })
        this.$message.success('新增学科成功')
        this.addDialogVisible = false
        this.getSubjectsList()
      } catch (e) {
        this.$message.error('新增学科失败')
      }
    },
    // 重置输入框
    resetSub () {
      this.$refs.addSubjectsFromRef.resetFields()
    },
    // 修改学科
    async editSubFrom () {
      if (this.editSubjectsFrom.isFrontDisplay === true) {
        this.editSubjectsFrom.isFrontDisplay = 1
      } else {
        this.editSubjectsFrom.isFrontDisplay = 0
      }
      try {
        await update ({
          id: this.userId,
          subjectName: this.editSubjectsFrom.subjectName,
          isFrontDisplay: this.editSubjectsFrom.isFrontDisplay
        })
        this.$message.success('修改学科成功')
        this.editDialogVisible = false
        this.getSubjectsList()
      } catch (e) {
        this.$message.error('修改学科失败')
      }
    },
    // 修改清空
    editResetSub () {
      this.$refs.editSubjectsFromRef.resetFields()
    },
    // 点击修改显示弹框
    editSubList (info) {
      this.editSubjectsFrom.subjectName = info.subjectName
      if (info.isFrontDisplay === 1) {
        this.editSubjectsFrom.isFrontDisplay = true
      } else {
        this.editSubjectsFrom.isFrontDisplay = false
      }
      this.userId = info.id
      this.editDialogVisible = true
    },
    // 点击新增学科显示弹出框
    addSubjects () {
      this.addDialogVisible = true
    },
    //删除学科
     async delSubject (subject) {
    await this.$confirm('此操作将永久删除该学科, 是否继续?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning'
    })
    await remove(subject)
    this.$message.success('删除成功')
    this.getSubjectsList()
    },
    },
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
.fenyeyouyi {
  margin-top: 20px;
  height: 28px;
}
.el-pagination {
  position: absolute;
  right: 42px;
}
.subspan {
  margin: 0 12px;
}
</style>
