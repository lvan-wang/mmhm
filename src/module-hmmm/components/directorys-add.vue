<template>
  <div class='container'>
    <el-dialog
      :title="oneData.id ? '修改目录' : '新增目录'"
      :visible.sync="dialogVisible"
      width="410px">
      <!-- 表单 -->
      <el-form ref="dirForm" :model="reqParams" :rules="rules" label-width="80px">
        <!-- id不存在的时候，才可以让 选择学科显示 -->
        <el-form-item label="所属学科" v-if="!$route.query.id" prop="subjectID">
          <el-select v-model="reqParams.subjectID" placeholder="请选择" style="width: 100%">
            <el-option
              v-for="item in disciplineList"
              :key="item.value"
              :label="item.label"
              :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录名称" prop="directoryName">
          <el-input v-model="reqParams.directoryName" placeholder="请输入目录名称"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="determineSuccess">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
// 学科
import { simple } from '@/api/hmmm/subjects'
// 目录
import { add, update } from '@/api/hmmm/directorys'

export default {
  name: 'DirectoryAdd',
  props: ['oneData'],
  data () {
    return {
      dialogVisible: false,
      reqParams: {
        subjectID: null,
        directoryName: null
      },
      // 学科列表
      disciplineList: [],
      // 验证
      rules: {
        // 目录验证
        directoryName: [
          { required: true, message: '请输入目录名称', trigger: 'blur' }
        ],
        // 学科验证
        subjectID: [
          { required: true, message: '请选择学科', trigger: 'change' }
        ]
      }
    }
  },
  created () {
    this.getSubject()
  },
  methods: {
    // 打开弹层方法
    open () {
      // 如果有id那就是修改，需要数据回填
      if (this.oneData.id) {
        // 数据回填
        this.reqParams.subjectID = this.oneData.subjectID
        this.reqParams.directoryName = this.oneData.directoryName
      } else {
        // 没有id就先把数据清空
        this.reqParams.subjectID = null
        this.reqParams.directoryName = null
      }
      this.dialogVisible = true
      // 清除效验效果 ,需要写再打开弹出下面
      this.$nextTick(() => {
        this.$refs.dirForm.clearValidate()
      })
    },
    // 获取学科
    async getSubject () {
      // 获取学科列表
      const { data } = await simple()
      this.disciplineList = data
    },
    // 确认成功
    determineSuccess () {
      // 全部触发效验
      this.$refs.dirForm.validate(async (valid) => {
        if (valid) {
          // 获取的学科 id，赋值给学科字段
          if (this.$route.query.id) {
            // 注意这里需要把 id转为整数
            this.reqParams.subjectID = +this.$route.query.id
          }
          if (this.oneData.id) {
            try {
              await update({ id: this.oneData.id, ...this.reqParams })
              this.$message.success('修改目录成功!')
            } catch (e) {
              this.$message.success('修改目录失败!')
            }
          } else {
            try {
              await add(this.reqParams)
              this.$message.success('添加目录成功!')
            } catch (e) {
              this.$message.success('添加目录失败!')
            }
          }
          this.dialogVisible = false
          // 无论是添加成功还是编辑成功，都通知父组件重新渲染
          this.$emit('addSuccess')
        }
      })
    }
  }
}
</script>

<style lang='scss'>
.el-dialog__footer {
  text-align: right;
}
</style>