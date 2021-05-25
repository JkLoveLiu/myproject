<template>
  <div id="app">

    <transition name="el-fade-in">
      <log-html v-if="showLogHtml"></log-html>
    </transition>
    <transition name="el-fade-in">
      <admin-html v-if="showAdminHtml"></admin-html>
    </transition>

  </div>
</template>

<script>
import LogHtml from "./components/logHtml";
import axios from "axios";
import AdminHtml from "./components/adminHtml";

export default {
  name: 'App',
  components: {AdminHtml, LogHtml},
  data() {
    return {
      showLogHtml: false,
      showAdminHtml: false
    }
  },
  methods: {
    hideLogHtml() {
      this.showLogHtml = false;
      this.showAdminHtml = true;
    }

  },
  created() {
    axios
      .get('./php/log/isLog.php')
      .then((res) => {
        if (res.data === 2) {
          // 未登录
          this.showLogHtml = true;
        } else {
          // 已登录
          this.showAdminHtml = true;
        }
      })
      .catch((error) => { // 请求失败处理
        console.log(error);
        this.$notify.error({
          title: '数据请求失败',
          message: '后台数据请求失败，请检查服务器！'
        });
      });
  }
}
</script>

<style>
#app {
  height: 100vh;
  width: 100vw;
  position: absolute;
  left: 0;
  top: 0;
}
</style>
