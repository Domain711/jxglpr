<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="工号" prop="num">
        <el-input v-model="dataForm.num" placeholder="工号"></el-input>
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
        <el-select v-model="dataForm.majornum" placeholder="请选择专业" @change="getGradeListByMajorId">
          <el-option
            v-for="major in majorList"
            :key="major.majornum"
            :label="major.majorname"
            :value="major.majornum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="教课班级" prop="gradenum">
        <el-select v-model="dataForm.gradenum" placeholder="请选择班级">
          <el-option
            v-for="grade in gradeList"
            :key="grade.gradenum"
            :label="grade.gradename"
            :value="grade.gradenum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="姓名" prop="name">
        <el-input v-model="dataForm.name" placeholder="姓名"></el-input>
      </el-form-item>
      <el-form-item label="课程" prop="coursenum">
        <el-select v-model="dataForm.coursenum" placeholder="请选择课程">
          <el-option
            v-for="course in courseList"
            :key="course.coursenum"
            :label="course.coursename"
            :value="course.coursenum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="学时" prop="period">
        <el-input v-model="dataForm.period" placeholder="学时"></el-input>
      </el-form-item>
      <el-form-item label="学分" prop="credit">
        <el-input v-model="dataForm.credit" placeholder="学分"></el-input>
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
        gradeList: [],
        courseList: [],
        dataForm: {
          id: 0,
          num: '',
          collegenum: '',
          majornum: '',
          gradenum: '',
          name: '',
          coursenum: '',
          type: '',
          period: '',
          credit: ''
        },
        dataRule: {
          num: [
            { required: true, message: '工号不能为空', trigger: 'blur' }
          ],
          collegenum: [
            { required: true, message: '学院编号不能为空', trigger: 'blur' }
          ],
          majornum: [
            { required: true, message: '专业编号不能为空', trigger: 'blur' }
          ],
          gradenum: [
            { required: true, message: '教课班级编号不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '姓名不能为空', trigger: 'blur' }
          ],
          coursenum: [
            { required: true, message: '教授课程编号不能为空', trigger: 'blur' }
          ],
          period: [
            { required: true, message: '学时不能为空', trigger: 'blur' }
          ],
          credit: [
            { required: true, message: '学分不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        API.college.select().then(({data}) => {
          this.collegeList = data && data.code === 0 ? data.collegeList : []
          var params = {
            'collegenum': this.dataForm.collegenum
          }
          API.major.select(params).then(({data}) => {
            this.majorList = data.majorList
          })
          var params1 = {
            'majornum': this.dataForm.majornum
          }
          API.grade.select(params1).then(({data}) => {
            this.gradeList = data.gradeList
          })
          API.course.select(params1).then(({data}) => {
            this.courseList = data.courseList
          })
        }).then(() => {
          this.visible = true
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
          })
        }).then(() => {
          if (this.dataForm.id) {
            API.teachers.info(this.dataForm.id).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.num = data.teachers.num
                this.dataForm.collegenum = data.teachers.collegenum
                this.dataForm.majornum = data.teachers.majornum
                this.dataForm.gradenum = data.teachers.gradenum
                this.dataForm.name = data.teachers.name
                this.dataForm.coursenum = data.teachers.coursenum
                this.dataForm.type = data.teachers.type
                this.dataForm.period = data.teachers.period
                this.dataForm.credit = data.teachers.credit
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

      // 根据专业id获取班级信息
      getGradeListByMajorId () {
        var params = {
          'majornum': this.dataForm.majornum
        }
        API.grade.select(params).then(({data}) => {
          this.gradeList = data.gradeList
        })
        API.course.select(params).then(({data}) => {
          this.courseList = data.courseList
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            var params = {
              'id': this.dataForm.id || undefined,
              'num': this.dataForm.num,
              'collegenum': this.dataForm.collegenum,
              'majornum': this.dataForm.majornum,
              'gradenum': this.dataForm.gradenum,
              'name': this.dataForm.name,
              'coursenum': this.dataForm.coursenum,
              'type': this.dataForm.type,
              'period': this.dataForm.period,
              'credit': this.dataForm.credit
            }
            var tick = !this.dataForm.id ? API.teachers.add(params) : API.teachers.update(params)
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
