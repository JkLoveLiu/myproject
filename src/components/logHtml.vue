<template>
  <div id="bgId">
    <div class="center-vh" >
      <div class="bg-translucent p-2" @keyup.enter="ajaxPostLog()">
        <div class="flex-box bg-white login-box">
          <div class="login-left p-5">
            <form action="http://localhost/php/log/log.php" method="post" class="login-form">
              <div class="form-group has-feedback">
                <span class="mdi mdi-account" aria-hidden="true"></span>
                <input type="text" class="form-control" id="username" placeholder="账号" v-model="form.username">
              </div>
              <div class="form-group has-feedback">
                <span class="mdi mdi-lock" aria-hidden="true"></span>
                <input type="password" class="form-control" id="password" placeholder="密码" v-model="form.pwd">
              </div>
              <div class="form-group has-feedback row">
                <div class="col-7">
                  <span class="mdi mdi-check-all form-control-feedback" aria-hidden="true"></span>
                  <input type="text" v-model="form.captcha" class="form-control" placeholder="验证码">
                </div>
                <div class="col-5 text-right">
                  <img :src="form.captchaSrc" class="pull-right" id="captcha" style="cursor: pointer;"
                       onclick="this.src=this.src+'?d='+Math.random();" title="点击刷新" alt="captcha">
                </div>
              </div>
              <div class="form-group">
                <button class="btn btn-block btn-primary" type="button" @click="ajaxPostLog()">立即登录</button>
              </div>
            </form>
          </div>
          <div class="login-right p-5 d-none d-sm-block">
            <p class="text-white">这是一个管理系统的HTML模板，登录页基于Bootstrap v4.4.1，其他页面基于vue.js + element UI组件制作完成。</p>
            <p class="text-white align-self-end">Copyright © 2020 <a href="javascript:alert('啥也没有')">主页</a>. All
              right
              reserved</p>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<script>
import axios from "axios";

export default {
  name: "logHtml",
  data() {
    return {
      bgShow:{
        backgroundImage: "url('/assets/login-bg-3.jpg'))",
        backgroundSize: 'cover'
      },
      form: {
        username: '',
        pwd: '',
        captcha: '',
        captchaSrc:''
      }
    }
  },
  methods: {
    ajaxPostLog() {
      // 表单验证
      if (this.form.username.length === 0) {
        this.$message.error('请输入用户名');
      } else if (this.form.pwd.length === 0) {
        this.$message.error('请输入密码');
      } else if (this.form.captcha.length === 0) {
        this.$message.error('请输入验证码');
      } else if (this.form.username.length < 5 || this.form.username.length > 11) {
        this.$message.error('用户名长度在 5 到 11 个字符');
      } else if (this.form.pwd.length < 6 || this.form.pwd.length > 14) {
        this.$message.error('请正确输入密码长度在 6 到 14 个字符');
      } else if (this.form.captcha.length < 4 || this.form.captcha.length > 4) {
        this.$message.error('验证码长度 4 个字符');
        this.form.captchaSrc = this.form.captchaSrc+'?d='+Math.random()
      } else {
        axios({
          url: './php/log/log.php',
          method: 'post',
          // 数据格式
          data: 'username=' + this.form.username +
            '&pwd=' + this.form.pwd +
            '&captcha=' + this.form.captcha,
          // 修改请求头，防止后端接收不到数据
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          }
        }).then((res) => {
          if (res.data === 1) {
            this.form.captcha = ''
            this.$message.error('验证码错误');
            this.form.captchaSrc = this.form.captchaSrc+'?d='+Math.random()
          } else if (res.data === 2) {
            this.form.captcha = ''
            this.$message.error('用户名或密码错误');
            this.form.captchaSrc = this.form.captchaSrc+'?d='+Math.random()
          } else if (res.data === 3) {
            this.$message.success('登陆成功，即将跳转至主页面');
            setTimeout(()=>{
              this.$parent.hideLogHtml();
            },1200)
          } else if (res.data === 4) {
            this.$message.error('账号被禁用！请联系管理员。');
          }
        }).catch(function (error) { // 请求失败处理
          console.log(error);
        });
      }
    }
  },
  created() {
    this.form.captchaSrc = './php/yzm.php'
  }

}
</script>
<style src="../assets/css/bootstrap.min.css" scoped></style>
<style src="../assets/css/style.min.css" scoped></style>
<style src="../assets/css/materialdesignicons.min.css" scoped></style>
<style scoped>
#bgId{
  height: 100%;
  width: 100%;
  background-image: url('../assets/login-bg-3.jpg');
  background-size: cover;
}

.login-form .has-feedback {
  position: relative;
}

.login-form .has-feedback .form-control {
  padding-left: 36px;
}

.login-form .has-feedback .mdi {
  position: absolute;
  top: 0;
  left: 0;
  right: auto;
  width: 36px;
  height: 36px;
  line-height: 36px;
  z-index: 4;
  color: #dcdcdc;
  display: block;
  text-align: center;
  pointer-events: none;
}

.login-form .has-feedback.row .mdi {
  left: 15px;
}

.login-form .form-group:last-child,
.login-right p:last-child {
  margin-bottom: 0;
}

.login-right {
  background: #67b26f !important;
  background: -moz-linear-gradient(45deg, #67b26f 0, #4ca2cd 100%) !important;
  background: -webkit-linear-gradient(45deg, #67b26f 0, #4ca2cd 100%) !important;
  background: linear-gradient(45deg, #67b26f 0, #4ca2cd 100%) !important;
  filter: progid:DXImageTransform.Microsoft.gradient(startColorstr='#67b26f', endColorstr='#4ca2cd', GradientType=1) !important;
}

.login-box {
  max-width: 700px;
}

.login-right {
  max-width: 50%;
}

.center-vh {
  display: -webkit-box;
  display: flex;
  -webkit-box-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  align-items: center;
  height: 100%;
}

</style>
