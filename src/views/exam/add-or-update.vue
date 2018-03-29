<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
        <el-form-item label="所属学院" prop="collegenum">
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
      <el-form-item label="班级" prop="gradenum">
        <el-select v-model="dataForm.gradenum" placeholder="请选择班级">
          <el-option
            v-for="grade in gradeList"
            :key="grade.gradenum"
            :label="grade.gradename"
            :value="grade.gradenum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="学生" prop="studentnum">
        <el-input v-model="dataForm.studentnum" placeholder="学生学号"></el-input>
      </el-form-item>
      <el-form-item label="成绩" prop="score">
        <el-input v-model="dataForm.score" placeholder="考试成绩"></el-input>
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
        gradeList: [],
        courseList: [],
        dataForm: {
          id: 0,
          collegenum: '',
          majornum: '',
          coursenum: '',
          gradenum: '',
          studentnum: '',
          score: '',
          createtime: '',
          remark: ''
        },
        dataRule: {
          collegenum: [
            { required: true, message: '学院编号不能为空', trigger: 'blur' }
          ],
          majornum: [
            { required: true, message: '专业编号不能为空', trigger: 'blur' }
          ],
          coursenum: [
            { required: true, message: '课程编号不能为空', trigger: 'blur' }
          ],
          gradenum: [
            { required: true, message: '班级编号不能为空', trigger: 'blur' }
          ],
          studentnum: [
            { required: true, message: '学生学号不能为空', trigger: 'blur' }
          ],
          score: [
            { required: true, message: '考试成绩不能为空', trigger: 'blur' }
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
            API.exam.info(this.dataForm.id).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.collegenum = data.exam.collegenum
                this.dataForm.majornum = data.exam.majornum
                this.dataForm.coursenum = data.exam.coursenum
                this.dataForm.gradenum = data.exam.gradenum
                this.dataForm.studentnum = data.exam.studentnum
                this.dataForm.score = data.exam.score
                this.dataForm.createtime = data.exam.createtime
                this.dataForm.remark = data.exam.remark
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
              'collegenum': this.dataForm.collegenum,
              'majornum': this.dataForm.majornum,
              'coursenum': this.dataForm.coursenum,
              'gradenum': this.dataForm.gradenum,
              'studentnum': this.dataForm.studentnum,
              'score': this.dataForm.score,
              'createtime': this.dataForm.createtime,
              'remark': this.dataForm.remark
            }
            var tick = !this.dataForm.id ? API.exam.add(params) : API.exam.update(params)
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
