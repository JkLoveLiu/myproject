<template>
  <div class="templateWindow">
    <div style="position: absolute;left: 10px;top: 10px;width: 350px">
      <el-form :model="updatePwdRuleForm"
               :rules="updatePwdRules"
               ref="ruleForm"
               :label-position="updatePwdLabelPosition"
               class="demo-ruleForm">
        <el-form-item label="原密码" prop="oldPwd">
          <el-input v-model="updatePwdRuleForm.oldPwd" placeholder="请输入原密码"></el-input>
        </el-form-item>
        <el-form-item label="新密码" prop="newPwd">
          <el-input v-model="updatePwdRuleForm.newPwd" placeholder="请输入新密码"></el-input>
        </el-form-item>
        <el-form-item label="确认密码" prop="isNewPwd">
          <el-input v-model="updatePwdRuleForm.isNewPwd" placeholder="请输入确认密码"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
          <el-button type="primary" @click="submitForm('ruleForm')">修改密码</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "updatePwd",
  data() {
    return {
      /*
-----------------------------分割线------------------------------
               修改密码页数据
 */
      // 表单数据
      updatePwdRuleForm: {
        oldPwd: '',
        newPwd: '',
        isNewPwd: '',
      },
      // 表单验证规则
      updatePwdRules: {
        oldPwd: [
          {required: true, message: '请输入原密码', trigger: 'blur'},
          {min: 6, max: 14, message: '长度在 6 到 14 个字符', trigger: 'blur'}
        ],
        newPwd: [
          {required: true, message: '请输入新密码', trigger: 'blur'},
          {min: 6, max: 14, message: '长度在 6 到 14 个字符', trigger: 'blur'}
        ],
        isNewPwd: [
          {required: true, message: '请输入确认密码', trigger: 'blur'},
          {min: 6, max: 14, message: '长度在 6 到 14 个字符', trigger: 'blur'}
        ],
      },
      updatePwdLabelPosition: 'left',
    }
  },
  methods: {
    /*
-----------------------------分割线------------------------------
            修改密码页方法
*/
    // 修改密码页方法
    submitForm(formName) {
      let newPwd = this.updatePwdRuleForm.newPwd;
      let oldPwd = this.updatePwdRuleForm.oldPwd;
      let isNewPwd = this.updatePwdRuleForm.isNewPwd;
      if (newPwd.length === 0) {
        this.$message.error('请输入原密码')
      } else if (oldPwd.length === 0) {
        this.$message.error('请输入新密码')
      } else if (isNewPwd.length === 0) {
        this.$message.error('请输入确认密码')
      } else if (newPwd === oldPwd) {
        this.$message.error('新密码不能与原密码相同')
      } else {
        if (newPwd === isNewPwd) {
          this.$refs[formName].validate((valid) => {
            if (valid) {
              axios({
                url: './php/update/update_Pwd.php',
                method: 'post',
                // 数据格式
                data: 'oldPwd=' + oldPwd +
                  '&newPwd=' + newPwd +
                  '&isNewPwd=' + isNewPwd,
                // 修改请求头，防止后端接收不到数据
                headers: {
                  'Content-Type': 'application/x-www-form-urlencoded'
                }
              }).then((res) => {
                if (res.data === 7) {
                  this.$message.success('密码修改成功，即将重新登录');
                  setTimeout(()=>{
                    window.location.reload()
                  },1000)
                } else if (res.data === 8) {
                  this.$message.warning('原密码错误,请重试');
                  this.updatePwdRuleForm.isNewPwd = ''
                  this.updatePwdRuleForm.newPwd = ''
                  this.updatePwdRuleForm.oldPwd = ''
                }
              }).catch(function (error) { // 请求失败处理
                console.log(error);
              });
            } else {
              this.$message.error('请正确输入')
              return false;
            }
          });
        } else {
          this.$message.error('确认密码不正确')
        }
      }
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
      this.updatePwdRuleForm.isNewPwd = ''
      this.updatePwdRuleForm.newPwd = ''
      this.updatePwdRuleForm.oldPwd = ''
    },
  }
}
</script>

<style scoped>
.templateWindow {
  height: calc(100% - 40px);
  width: 100%;
  background-color: #D3DCE6;
  position: absolute;
  left: 0;
  top: 40px
}

.windowContent {
  position: absolute;
  left: 10px;
  top: 10px;
  width: calc(100% - 10px);
  height: calc(100% - 10px);
}
</style>
