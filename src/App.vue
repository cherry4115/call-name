<template>
  <div id = "app">
    <el-row style = "position: absolute; height: 30%; width: 90%; top: 7.5%; left: 4.5%; background: #838d82">
      <el-col :span = "9" style = "height: 100%; padding: 20px 30px 20px 20px; font-size: 18px">
        <div style = "color: white; margin-bottom: 10px">本节课已点同学：</div>
        <div style = "color: #ffd2d2">{{calledStudentsName[form.class] && calledStudentsName[form.class].join('，')}}</div>
      </el-col>

      <el-col :span = "6" style = "height: 100%">
        <div style = "margin-top: 80px; text-align: center; color: yellow; font-size: 90px">
          {{nowCalledStudentName}}
        </div>
      </el-col>

      <el-col :span = "9" style = "height: 100%;">
        <el-form 
          :inline = "true" 
          :model = "form" 
          style = "text-align: center; margin-top: 50px">

          <el-form-item>
            <el-select 
              v-model = "form.class" 
              :disabled = "isRuning" 
              placeholder = "请选择班级"
              @change = "handleClassChange">
              <el-option
                v-for = "(item, index) in allClass"
                :key = "index"
                :label = "item"
                :value = "index">
              </el-option>
            </el-select>
          </el-form-item>
          <br/><br/>

          <el-button 
            type = "danger"
            :disabled = "isRuning"
            @click = "handleStart">
            开始
          </el-button>

          <el-button 
            type = "danger" 
            style = "margin-left: 30px"
            :disabled = "!isRuning"
            @click = "handleStop">
            停止
          </el-button>

        </el-form>
      </el-col>
    </el-row>

  </div>
</template>

<script>
const NAME_DATA = require('./name.json')

export default {
  data () {
    return {
      form: {
        class: ''
      },
      allStudents: NAME_DATA.data,
      allClass: [],
      calledStudentsIndex: [],  // 二维
      calledStudentsName: [],  // 二维
      unCalledStudentsIndex: [],  // 二维
      nowCalledStudentIndex: -1,
      intervalObj: -1,
      isRuning: false
    }
  },

  computed: {
    nowCalledStudentName: function () {
      if (this.nowCalledStudentIndex === -1) {
        return ''
      } else {
        return this.allStudents[this.form.class].students[this.nowCalledStudentIndex].name
      }
    }
  },

  mounted () {
    document.getElementById('app').style.height = document.documentElement.clientHeight - 16 + 'px'
    this.allStudents.map(item => {
      this.allClass.push(item.class)
      let arr = [];
      for (let i = 0; i < item.students.length; i++) {
        arr.push(i)
      }
      this.unCalledStudentsIndex.push(arr)
      this.calledStudentsIndex.push([])
      this.calledStudentsName.push([])
    })
  },

  methods: {
    handleClassChange (value) {
      this.nowCalledStudentIndex = -1
    },

    handleStart () {
      if (this.form.class === '') {
        this.$message.error('先选择班级哦 O(∩_∩)O')
        return
      }
      this.isRuning = true
      let unCalledLength = this.unCalledStudentsIndex[this.form.class].length
      if (unCalledLength === 1) {
        this.$alert('只有这一位同学没被点过名了哦！', '提示', {
          confirmButtonText: 'OK',
          callback: action => { this.handleStop() }
        });
      }
      if (unCalledLength === 0) {
        this.$message.success('全班同学都已点完，将重新记录！')        
        this.unCalledStudentsIndex[this.form.class] = this.calledStudentsIndex[this.form.class].concat()
        this.calledStudentsIndex[this.form.class] = []
        this.calledStudentsName[this.form.class] = []
        unCalledLength = this.unCalledStudentsIndex[this.form.class].length
      }
      let In = setInterval(() => { 
        let r = Math.floor(Math.random() * unCalledLength)
        this.nowCalledStudentIndex = this.unCalledStudentsIndex[this.form.class][r]
      }, 100)
      this.intervalObj = In
    },

    handleStop () {
      this.isRuning = false
      clearInterval(this.intervalObj)
      this.calledStudentsIndex[this.form.class].push(this.nowCalledStudentIndex)
      this.calledStudentsName[this.form.class].push(this.allStudents[this.form.class].students[this.nowCalledStudentIndex].name)
      let i = this.unCalledStudentsIndex[this.form.class].indexOf(this.nowCalledStudentIndex)
      this.unCalledStudentsIndex[this.form.class].splice(i, 1)
    }
  }
}
</script>

<style>
#app {
  font-family: Helvetica, sans-serif;
  background-image: url(./assets/background.jpg);
  background-size: cover;
  position: relative;
}
</style>
