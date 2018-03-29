<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="班级编号" prop="gradenum">
        <el-input v-model="dataForm.gradenum" placeholder="班级编号"></el-input>
      </el-form-item>
      <el-form-item label="班级名称" prop="gradename">
        <el-input v-model="dataForm.gradename" placeholder="班级名称"></el-input>
      </el-form-item>
      <el-form-item label="学院" prop="collegenum">
        <el-select v-model="dataForm.collegenum" placeholder="请选择学院" @change="getMajorListByCollegeId">
          <el-option
            v-for="college in collegeList"
            :key="college.collegenum"
            :label="college.collegename"
            :value="college.collegenum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="专业" prop="majornum">
        <el-select v-model="dataForm.majornum" placeholder="请选择专业">
          <el-option
            v-for="major in majorList"
            :key="major.majornum"
            :label="major.majorname"
            :value="major.majornum">
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
        majorList: [],
        dataForm: {
          id: 0,
          gradenum: '',
          gradename: '',
          collegenum: '',
          majornum: '',
          createtime: '',
          createuser: '',
          remark: ''
        },
        dataRule: {
          gradenum: [
            { required: true, message: '班级编号不能为空', trigger: 'blur' }
          ],
          gradename: [
            { required: true, message: '班级名称不能为空', trigger: 'blur' }
          ],
          collegenum: [
            { required: true, message: '学院不能为空', trigger: 'blur' }
          ],
          majornum: [
            { required: true, message: '专业不能为空', trigger: 'blur' }
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
            API.grade.info(this.dataForm.id).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.gradenum = data.grade.gradenum
                this.dataForm.gradename = data.grade.gradename
                this.dataForm.collegenum = data.grade.collegenum
                this.dataForm.majornum = data.grade.majornum
                this.dataForm.createtime = data.grade.createtime
                this.dataForm.createuser = data.grade.createuser
                this.dataForm.remark = data.grade.remark
              }
            })
          }
        })
      },

      // 根据学院id获取专业信息
      getMajorListByCollegeId () {
        var params = {
          'collegenum': this.dataForm.collegenum
        }
        API.major.select(params).then(({data}) => {
          this.majorList = data.majorList
        })
      },

      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            var params = {
              'id': this.dataForm.id || undefined,
              'gradenum': this.dataForm.gradenum,
              'gradename': this.dataForm.gradename,
              'collegenum': this.dataForm.collegenum,
              'majornum': this.dataForm.majornum,
              'createtime': this.dataForm.createtime,
              'createuser': this.dataForm.createuser,
              'remark': this.dataForm.remark
            }
            var tick = !this.dataForm.id ? API.grade.add(params) : API.grade.update(params)
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
