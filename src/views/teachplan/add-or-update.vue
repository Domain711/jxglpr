<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="教案名称" prop="teachplanname">
        <el-input v-model="dataForm.teachplanname" placeholder="教案名称"></el-input>
      </el-form-item>
      <el-form-item label="附件名称" prop="attachname">
        <el-input v-model="dataForm.attachname" placeholder="附件名称"></el-input>
      </el-form-item>
      <el-form-item label="备注" prop="remark">
        <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import API from '@/api'
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          teachplanname: '',
          attachname: '',
          createuser: '',
          createtime: '',
          remark: ''
        },
        dataRule: {
          teachplanname: [
            { required: true, message: '教案名称不能为空', trigger: 'blur' }
          ],
          attachname: [
            { required: true, message: '附件名称不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            API.teachplan.info(this.dataForm.id).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.teachplanname = data.teachplan.teachplanname
                this.dataForm.attachname = data.teachplan.attachname
                this.dataForm.createuser = data.teachplan.createuser
                this.dataForm.createtime = data.teachplan.createtime
                this.dataForm.remark = data.teachplan.remark
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            var params = {
              'id': this.dataForm.id || undefined,
              'teachplanname': this.dataForm.teachplanname,
              'attachname': this.dataForm.attachname,
              'createuser': this.dataForm.createuser,
              'createtime': this.dataForm.createtime,
              'remark': this.dataForm.remark
            }
            var tick = !this.dataForm.id ? API.teachplan.add(params) : API.teachplan.update(params)
            tick.then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
