<template>
  <div class='audit-container'>
    <el-dialog
      title="题目审核"
      :visible.sync="dialogVisible"
      width="27%">
      <el-form ref="form" :model="auditForm" >
        <el-form-item>
          <el-radio-group v-model="auditForm.chkState">
            <el-radio :label="1">通过</el-radio>
            <el-radio :label="2">拒绝</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item>
          <el-input type="textarea" v-model="auditForm.chkRemarks" placeholder="请输入审核意见"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="isApprove">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { choiceCheck } from '@/api/hmmm/questions'
export default {
  name: 'Audit',
  props: ['auditId'],
  data () {
    return {
      dialogVisible: false,
      auditForm: {
        chkState: 1,
        chkRemarks: null
      }
    }
  },
  methods: {
    openDialog () {
      // 把父组件传的id，保存到参数对象里
      this.auditForm = { id: this.auditId, ...this.auditForm }
      this.dialogVisible = true
    },
    // 点击审核
    async isApprove () {
      // 如果没有输入内容就不让发送请求并提示
      if (!this.auditForm.chkRemarks) {
        this.$message.warning('请输入审核意见')
        return
      } else {
        await choiceCheck(this.auditForm)
        this.$message.success('审核成功!')
      }
      this.$emit('approve')
      this.dialogVisible = false
    }
  }
}
</script>

<style scoped lang='scss'>
::v-deep .el-dialog__footer {
  text-align: right;
}
</style>
