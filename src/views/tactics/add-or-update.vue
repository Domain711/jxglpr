<template>
  <el-dialog
    :title="!dataForm.id ? '组卷策略选择' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="学院编号" prop="collegenum">
        <el-select v-model="dataForm.collegenum" placeholder="请选择学院" @change="getMajorListByCollegeId">
          <el-option
            v-for="college in collegeList"
            :key="college.collegenum"
            :label="college.collegename"
            :value="college.collegenum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="专业编号" prop="majornum">
        <el-select v-model="dataForm.majornum" placeholder="请选择专业" @change="getGradeListByMajorId">
          <el-option
            v-for="major in majorList"
            :key="major.majornum"
            :label="major.majorname"
            :value="major.majornum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="课程编号" prop="coursenum">
        <el-select v-model="dataForm.coursenum" placeholder="请选择课程">
          <el-option
            v-for="course in courseList"
            :key="course.coursenum"
            :label="course.coursename"
            :value="course.coursenum">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="题型" size="mini" prop="qtypeList">
        <el-checkbox-group v-model="dataForm.qtypeList">
          <el-checkbox v-for="qtype in questiontypeList" :key="qtype.questiontype" :label="qtype.questiontype">{{ qtype.questiontypename }}</el-checkbox>
        </el-checkbox-group>
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
        courseList: [],
        questiontypeList: [],
        dataForm: {
          tacid: 0,
          collegenum: '',
          majornum: '',
          coursenum: '',
          content: '',
          qtypeList: ''
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
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.tacid = id || 0
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
          API.course.select(params1).then(({data}) => {
            this.courseList = data.courseList
          })
          API.questiontype.select().then(({data}) => {
            this.questiontypeList = data.questiontypeList
          })
        }).then(() => {
          this.visible = true
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
          })
        }).then(() => {
          if (this.dataForm.tacid) {
            API.tactics.info(this.dataForm.tacid).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.tacname = data.tactics.tacname
                this.dataForm.collegenum = data.tactics.collegenum
                this.dataForm.collegename = data.tactics.collegename
                this.dataForm.majornum = data.tactics.majornum
                this.dataForm.majorname = data.tactics.majorname
                this.dataForm.coursenum = data.tactics.coursenum
                this.dataForm.coursename = data.tactics.coursename
                this.dataForm.content = data.tactics.content
                this.dataForm.createid = data.tactics.createid
                this.dataForm.createtime = data.tactics.createtime
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
              'tacid': this.dataForm.tacid || undefined,
              'collegenum': this.dataForm.collegenum,
              'collegename': this.dataForm.collegename,
              'majornum': this.dataForm.majornum,
              'coursenum': this.dataForm.coursenum,
              'content': this.dataForm.content
            }
            var tick = !this.dataForm.tacid ? API.tactics.add(params) : API.tactics.update(params)
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
