<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
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
      <el-form-item label="题型分布" prop="qtype">
          <el-select v-model="dataForm.qtype" placeholder="请选择题型">
            <el-option
              v-for="questiontype in questiontypeList"
              :key="questiontype.questiontype"
              :label="questiontype.questiontypename"
              :value="questiontype.questiontype">
            </el-option>
          </el-select>
          <el-input v-model="dataForm.c2" placeholder="试题数" style="width: 80px"></el-input>
          <el-input v-model="dataForm.f2" placeholder="分数" style="width: 80px"></el-input>
          <el-input v-model="dataForm.z2" placeholder="知识点" style="width: 120px"></el-input>
        <el-select v-model="dataForm.qtype2" placeholder="请选择题型">
          <el-option
            v-for="questiontype in questiontypeList"
            :key="questiontype.questiontype"
            :label="questiontype.questiontypename"
            :value="questiontype.questiontype">
          </el-option>
        </el-select>
        <el-input v-model="dataForm.c3" placeholder="试题数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.f3" placeholder="分数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.z3" placeholder="知识点" style="width: 120px"></el-input>
        <el-select v-model="dataForm.qtype3" placeholder="请选择题型">
          <el-option
            v-for="questiontype in questiontypeList"
            :key="questiontype.questiontype"
            :label="questiontype.questiontypename"
            :value="questiontype.questiontype">
          </el-option>
        </el-select>
        <el-input v-model="dataForm.c4" placeholder="试题数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.f4" placeholder="分数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.z4" placeholder="知识点" style="width: 120px"></el-input>
        <el-select v-model="dataForm.qtype4" placeholder="请选择题型">
          <el-option
            v-for="questiontype in questiontypeList"
            :key="questiontype.questiontype"
            :label="questiontype.questiontypename"
            :value="questiontype.questiontype">
          </el-option>
        </el-select>
        <el-input v-model="dataForm.c5" placeholder="试题数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.f5" placeholder="分数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.z5" placeholder="知识点" style="width: 120px"></el-input>
        <el-select v-model="dataForm.qtype5" placeholder="请选择题型">
          <el-option
            v-for="questiontype in questiontypeList"
            :key="questiontype.questiontype"
            :label="questiontype.questiontypename"
            :value="questiontype.questiontype">
          </el-option>
        </el-select>
        <el-input v-model="dataForm.c6" placeholder="试题数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.f6" placeholder="分数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.z6" placeholder="知识点" style="width: 120px"></el-input>
        <el-select v-model="dataForm.qtype6" placeholder="请选择题型">
          <el-option
            v-for="questiontype in questiontypeList"
            :key="questiontype.questiontype"
            :label="questiontype.questiontypename"
            :value="questiontype.questiontype">
          </el-option>
        </el-select>
        <el-input v-model="dataForm.c7" placeholder="试题数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.f7" placeholder="分数" style="width: 80px"></el-input>
        <el-input v-model="dataForm.z7" placeholder="知识点" style="width: 120px"></el-input>
      </el-form-item>
      <el-form-item label="试卷标题" prop="weight">
        <el-input v-model="dataForm.bt" placeholder="试卷标题"></el-input>
      </el-form-item>
      <el-form-item label="考试时间" prop="weight">
        <el-date-picker
          v-model="dataForm.sj"
          type="datetime"
          placeholder="选择日期时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="满分值" prop="weight">
        <el-input v-model="dataForm.mfz" placeholder="满分值"></el-input>
      </el-form-item>
      <el-form-item label="平均难度" prop="weight">
        <el-input v-model="dataForm.pjnd" placeholder="平均难度"></el-input>
      </el-form-item>
      <el-form-item label="均区分度" prop="weight">
        <el-input v-model="dataForm.pjqfd" placeholder="均区分度"></el-input>
      </el-form-item>
      <el-form-item label="知识点" prop="weight">
        <el-input v-model="dataForm.kczsd" placeholder="知识点"></el-input>
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
          tacname: '',
          collegenum: '',
          collegename: '',
          majornum: '',
          majorname: '',
          coursenum: '',
          coursename: '',
          content: '',
          createid: '',
          createtime: '',
          qtype: '',
          weight: ''
        },
        dataRule: {
          tacname: [
            { required: true, message: '试卷名称不能为空', trigger: 'blur' }
          ],
          collegenum: [
            { required: true, message: '学院编号不能为空', trigger: 'blur' }
          ],
          collegename: [
            { required: true, message: '学院不能为空', trigger: 'blur' }
          ],
          majornum: [
            { required: true, message: '专业编号不能为空', trigger: 'blur' }
          ],
          majorname: [
            { required: true, message: '专业名称不能为空', trigger: 'blur' }
          ],
          coursenum: [
            { required: true, message: '课程编号不能为空', trigger: 'blur' }
          ],
          coursename: [
            { required: true, message: '课程名称不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '试卷内容不能为空', trigger: 'blur' }
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
                this.dataForm.qtype = data.tactics.qtype
                this.dataForm.weight = data.tactics.weight
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
              'tacname': this.dataForm.tacname,
              'collegenum': this.dataForm.collegenum,
              'collegename': this.dataForm.collegename,
              'majornum': this.dataForm.majornum,
              'majorname': this.dataForm.majorname,
              'coursenum': this.dataForm.coursenum,
              'coursename': this.dataForm.coursename,
              'content': this.dataForm.content,
              'createid': this.dataForm.createid,
              'createtime': this.dataForm.createtime,
              'qtype': this.dataForm.qtype,
              'weight': this.dataForm.weight
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
