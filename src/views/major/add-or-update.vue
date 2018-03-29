<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="专业编号" prop="majornum">
        <el-input v-model="dataForm.majornum" placeholder="专业编号"></el-input>
      </el-form-item>
      <el-form-item label="专业名称" prop="majorname">
        <el-input v-model="dataForm.majorname" placeholder="专业名称"></el-input>
      </el-form-item>
      <el-form-item label="所属学院" prop="collegenum">
        <el-select v-model="dataForm.collegenum" placeholder="请选择学院">
          <el-option
            v-for="college in collegeList"
            :key="college.collegenum"
            :label="college.collegename"
            :value="college.collegenum">
          </el-option>
        </el-select>
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
        collegeList: [],
        colleges: [{
          value: '01',
          label: '计算机学院'
        }, {
          value: '02',
          label: '电气学院'
        }, {
          value: '03',
          label: '土木工程学院'
        }, {
          value: '04',
          label: '外国语学院'
        }, {
          value: '05',
          label: '测勘院'
        }],
        dataForm: {
          id: 0,
          majornum: '',
          majorname: '',
          collegenum: '',
          createtime: '',
          createuser: '',
          remark: ''
        },
        dataRule: {
          majornum: [
            { required: true, message: '专业编号不能为空', trigger: 'blur' }
          ],
          majorname: [
            { required: true, message: '专业不能为空', trigger: 'blur' }
          ],
          collegenum: [
            { required: true, message: '学院不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        API.college.select().then(({data}) => {
          this.collegeList = data && data.code === 0 ? data.collegeList : []
        }).then(() => {
          this.visible = true
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
          })
        }).then(() => {
          if (this.dataForm.id) {
            API.major.info(this.dataForm.id).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.majornum = data.major.majornum
                this.dataForm.majorname = data.major.majorname
                this.dataForm.collegenum = data.major.collegenum
                this.dataForm.createtime = data.major.createtime
                this.dataForm.createuser = data.major.createuser
                this.dataForm.remark = data.major.remark
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
              'majornum': this.dataForm.majornum,
              'majorname': this.dataForm.majorname,
              'collegenum': this.dataForm.collegenum,
              'createtime': this.dataForm.createtime,
              'createuser': this.dataForm.createuser,
              'remark': this.dataForm.remark
            }
            var tick = !this.dataForm.id ? API.major.add(params) : API.major.update(params)
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
