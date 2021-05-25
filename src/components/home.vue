<template>
  <div class="templateWindow">
    <div class="windowContent">
      <div class="row">
        <div class="row_one">
          <div class="row_1">
            <span class="icon_local">
              <i class="el-icon-user"></i>
            </span>
            <span class="span_text">{{ todayAllClient }}</span>
          </div>
          <span class="row_2">客户总数</span>
        </div>
        <div class="row_two">
          <div class="row_1">
            <span class="icon_local">
              <i class="el-icon-plus"></i>
            </span>
            <span class="span_text">{{ todayInsert }}</span>
          </div>
          <span class="row_2">今日新增</span>
        </div>
        <div class="row_three">
          <div class="row_1">
            <span class="icon_local">
              <i class="el-icon-check"></i>
            </span>
            <span class="span_text">{{ todayComplete }}</span>
          </div>
          <span class="row_2">今日完成</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "home",
  data() {
    return {
      todayAllClient: '99999',
      todayInsert: '88888',
      todayComplete: '77777',
    }
  },
  methods: {
    getServerDate() {
      axios
        .get('./php/select/select_homeData.php')
        .then((res) => {
          console.log(res.data)
          this.todayAllClient = res.data.allClient
          this.todayInsert = res.data.newInsert
          this.todayComplete = res.data.newComplete
        })
        .catch(function (error) { // 请求失败处理
          console.log(error);
        });
    }
  },
  created() {
    this.getServerDate();
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

.row div {
  height: 120px;
  width: 350px;
  float: left;
  margin: 15px 25px;
  position: relative;
  box-shadow: 0 2px 3px rgb(0, 0, 4%);
}

.row .row_one {
  background: #33cabb;
  border-radius: 4px;
}

.row .row_two {
  background: #f96868;
  border-radius: 4px;
}

.row .row_three {
  background: #15c377;
  border-radius: 4px;
}

.row_1 {
  position: absolute;
  top: 20px;
  left: 0;
}

.icon_local {
  font-size: 25px;
  display: inline-block;
  height: 48px;
  width: 48px;
  background: rgba(255, 255, 255, 0.175);
  border-radius: 100px;
  line-height: 46px;
  text-align: center;
  color: white;
  font-weight: 900 !important;
}

.row_2 {
  position: absolute;
  right: 20px;
  bottom: 25px;
  color: white;
}

.span_text {
  display: inline-block;
  position: absolute;
  right: 45px;
  font-size: 22px;
  font-weight: 500;
  color: white;
  letter-spacing: 2px;
  font-family: "Microsoft YaHei", sans-serif;
}
</style>
