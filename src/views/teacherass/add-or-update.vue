<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="教师工号" prop="teachernum">
        <el-input v-model="dataForm.teachernum" placeholder="被评价教师工号"></el-input>
      </el-form-item>
      <el-form-item label="评价等级" prop="asslevel">
        <el-select v-model="dataForm.asslevel" placeholder="请选择学期">
          <el-option
            v-for="level in levelOptions"
            :key="level.value"
            :label="level.label"
            :value="level.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="学期" prop="term">
        <el-select v-model="dataForm.term" placeholder="请选择学期">
          <el-option
            v-for="term in termOptions"
            :key="term.value"
            :label="term.label"
            :value="term.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="评价备注" prop="assremark">
        <el-input v-model="dataForm.assremark" placeholder="评价备注"></el-input>
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
        termOptions: [{
          value: '1',
          label: '上学期'
        }, {
          value: '2',
          label: '下学期'
        }],
        levelOptions: [{
          value: '3',
          label: '优秀'
        }, {
          value: '2',
          label: '良好'
        }, {
          value: '1',
          label: '可接受'
        }, {
          value: '0',
          label: '需改进'
        }, {
          value: '-1',
          label: '不接受'
        }
        ],
        options: [{
          value: '1',
          label: '上学期'
        }, {
          value: '2',
          label: '下学期'
        }],
        dataForm: {
          id: 0,
          teachernum: '',
          asstype: '',
          asslevel: '',
          assvalue: '',
          assnum: '',
          asstime: '',
          term: '',
          assremark: ''
        },
        dataRule: {
          teachernum: [
            { required: true, message: '被评价教师工号不能为空', trigger: 'blur' }
          ],
          asstype: [
            { required: true, message: '人员类型 学生、老师不能为空', trigger: 'blur' }
          ],
          asslevel: [
            { required: true, message: '评价等级 优秀、良好、可接受不能为空', trigger: 'blur' }
          ],
          assvalue: [
            { required: true, message: '评价分值不能为空', trigger: 'blur' }
          ],
          assnum: [
            { required: true, message: '评价工号/学号不能为空', trigger: 'blur' }
          ],
          asstime: [
            { required: true, message: '评价时间不能为空', trigger: 'blur' }
          ],
          term: [
            { required: true, message: '学期不能为空', trigger: 'blur' }
          ],
          assremark: [
            { required: true, message: '评价备注不能为空', trigger: 'blur' }
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
            API.teacherass.info(this.dataForm.id).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.teachernum = data.teacherass.teachernum
                this.dataForm.asstype = data.teacherass.asstype
                this.dataForm.asslevel = data.teacherass.asslevel
                this.dataForm.assvalue = data.teacherass.assvalue
                this.dataForm.assnum = data.teacherass.assnum
                this.dataForm.asstime = data.teacherass.asstime
                this.dataForm.term = data.teacherass.term
                this.dataForm.assremark = data.teacherass.assremark
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
              'teachernum': this.dataForm.teachernum,
              'asstype': this.dataForm.asstype,
              'asslevel': this.dataForm.asslevel,
              'assvalue': this.dataForm.assvalue,
              'assnum': this.dataForm.assnum,
              'asstime': this.dataForm.asstime,
              'term': this.dataForm.term,
              'assremark': this.dataForm.assremark
            }
            var tick = !this.dataForm.id ? API.teacherass.add(params) : API.teacherass.update(params)
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
