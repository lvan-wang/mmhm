<template>
  <div class="add-form">
    <el-dialog :title="titleInfo.text+titleInfo.pageTitle" :visible.sync="dialogFormVisible">
      <el-form
        :rules="ruleInline"
        ref="dataForm"
        :model="formBase"
        label-position="left"
        label-width="150px"
        style="width: 80%; margin-left:10px;"
      >
      <el-form-item label="所属学科" prop="subjectName">
          <el-select
            class="filter-item"
            style="width: 130px;"
            v-model="formBase.subjectName"
            filterable
          >
            <el-option v-for="(item, index) in subjectList" :key="index" :value="item.value" :label="item.label"></el-option>
          </el-select>
          </el-form-item>
      <el-form-item label="标签名称" prop="tagName">
          <el-input v-model="formBase.tagName"></el-input>
      </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormH">{{$t('table.cancel')}}</el-button>
        <el-button type="primary" @click="createData">{{$t('table.confirm')}}</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects'
import { add, update } from '@/api/hmmm/tags'

export default {
  name: 'TagsAdd',
  props: {
    titleInfo: {
      type: Object,
      required: true
    },
    formBase: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      dialogFormVisible: false,
      // 学科简单列表
      subjectList: [],
      // 表单验证
      ruleInline: {
        tagName: [
          { required: true, message: '目录名称不能为空', trigger: 'blur' }
        ]
      },
      formBase: {
        subjectName: ''
      }
    }
  },
  computed: {},
  created () {
    this.getSubList()
  },
  mounted () {},
  methods: {
    // 弹层显示
    dialogFormV () {
      this.dialogFormVisible = true
    },
    // 弹层隐藏
    dialogFormH () {
      this.dialogFormVisible = false
    },
    // 获取学科列表
    async getSubList () {
      const { data } = await simple()
      this.subjectList = data
      console.log(this.subjectList)
    },
    // 点击确认按钮，执行添加 编辑确认操作
    async createData () {
      this.$refs.dataForm.validate(async valid => {
        if (valid) {
          this.dialogFormH()
          const data = {
            ...this.formBase
          }
          if (this.formBase.id) {
            await update(data).then(() => {
              this.$emit('newDataes', this.formBase)
            })
          } else {
            await add({
              subjectID: this.formBase.subjectName,
              tagName: this.formBase.tagName
            }).then(() => {
              this.$emit('newDataes', this.formBase)
            })
          }
        } else {
          this.$message.error('*号为必填项!')
        }
      })
    }
  }
}
</script>

<style scoped lang='less'></style>
