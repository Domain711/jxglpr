<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="学期" prop="term">
        <el-select v-model="dataForm.term" placeholder="请选择学期">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
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
      <el-form-item label="始时间" prop="starttime">
        <el-input v-model="dataForm.starttime" placeholder="考试开始时间"></el-input>
      </el-form-item>
      <el-form-item label="结束时间" prop="endtime">
        <el-input v-model="dataForm.endtime" placeholder="考试结束时间"></el-input>
      </el-form-item>
      <el-form-item label="考试地点" prop="addr">
        <el-input v-model="dataForm.addr" placeholder="考试地点"></el-input>
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
        options: [{
          value: '1',
          label: '上学期'
        }, {
          value: '2',
          label: '下学期'
        }],
        dataForm: {
          id: 0,
          term: '',
          collegenum: '',
          majornum: '',
          gradenum: '',
          coursenum: '',
          starttime: '',
          endtime: '',
          addr: '',
          createtime: '',
          createuser: '',
          remark: ''
        },
        dataRule: {
          term: [
            { required: true, message: '学期不能为空', trigger: 'blur' }
          ],
          collegenum: [
            { required: true, message: '学院编号不能为空', trigger: 'blur' }
          ],
          majornum: [
            { required: true, message: '专业编号不能为空', trigger: 'blur' }
          ],
          gradenum: [
            { required: true, message: '班级编号不能为空', trigger: 'blur' }
          ],
          coursenum: [
            { required: true, message: '课程编号不能为空', trigger: 'blur' }
          ],
          starttime: [
            { required: true, message: '考试开始时间不能为空', trigger: 'blur' }
          ],
          endtime: [
            { required: true, message: '考试结束时间不能为空', trigger: 'blur' }
          ],
          addr: [
            { required: true, message: '考试地点不能为空', trigger: 'blur' }
          ],
          createtime: [
            { required: true, message: '计划创建时间不能为空', trigger: 'blur' }
          ],
          createuser: [
            { required: true, message: '计划创建人不能为空', trigger: 'blur' }
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
            API.testplan.info(this.dataForm.id).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.term = data.testplan.term
                this.dataForm.collegenum = data.testplan.collegenum
                this.dataForm.majornum = data.testplan.majornum
                this.dataForm.gradenum = data.testplan.gradenum
                this.dataForm.coursenum = data.testplan.coursenum
                this.dataForm.starttime = data.testplan.starttime
                this.dataForm.endtime = data.testplan.endtime
                this.dataForm.addr = data.testplan.addr
                this.dataForm.createtime = data.testplan.createtime
                this.dataForm.createuser = data.testplan.createuser
                this.dataForm.remark = data.testplan.remark
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
              'term': this.dataForm.term,
              'collegenum': this.dataForm.collegenum,
              'majornum': this.dataForm.majornum,
              'gradenum': this.dataForm.gradenum,
              'coursenum': this.dataForm.coursenum,
              'starttime': this.dataForm.starttime,
              'endtime': this.dataForm.endtime,
              'addr': this.dataForm.addr,
              'createtime': this.dataForm.createtime,
              'createuser': this.dataForm.createuser,
              'remark': this.dataForm.remark
            }
            var tick = !this.dataForm.id ? API.testplan.add(params) : API.testplan.update(params)
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
