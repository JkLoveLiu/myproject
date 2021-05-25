<template>
  <div>
    <div style="margin-bottom: 10px">
      <el-switch
        v-model="isTheme"
        @change="mapDark"
        active-text="深色模式"
        inactive-text="浅色模式"
        active-color="#13ce66"
        inactive-color="#ff4949">
      </el-switch>
      <span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;时间选择</span>
      <el-date-picker
        v-model="dateDay"
        type="date"
        size="small"
        value-format="yyyy-MM-dd"
        @change="dateChange"
        placeholder="选择日期">
      </el-date-picker>
    </div>
    <div ref="main" id="main" style="width: 500px;height:500px;"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts/core';
import {
  GridComponent
} from 'echarts/components';
import {
  BarChart
} from 'echarts/charts';
import {
  CanvasRenderer
} from 'echarts/renderers';
import axios from "axios";

echarts.use(
  [GridComponent, BarChart, CanvasRenderer]
);

let myChart;
export default {
  name: "mapDateDay",
  data() {
    return {
      dateValue: [],
      isTheme: false,
      theme: '',
      dateDay: '',
      insert:'',
      complete:'',
    }
  },
  methods: {
    dateChange(d) {
      this.getServerDate()
      this.createCountMap();
    },
    mapDark(sw) {
      this.isTheme = sw;
      this.isTheme ? this.theme = 'dark' : this.theme = ''
      this.createCountMap()
    },
    createCountMap() {
      if (myChart != null && myChart !== "" && myChart !== undefined) {
        myChart.dispose();
      }
      // 基于准备好的dom，初始化echarts实例
      myChart = echarts.init(this.$refs.main, this.theme);

      let option = {
        xAxis: {
          type: 'category',
          data: ['新增客户', '新增完成客户']
        },
        yAxis: {
          type: 'value'
        },
        series: [{
          data: [this.insert, this.complete],
          type: 'bar',
          showBackground: true,
          backgroundStyle: {
            color: 'rgba(180, 180, 180, 0.2)'
          }
        }]
      };

      option && myChart.setOption(option);
    },
    getServerDate() {
      axios
        .get('./php/select/select_mapDateTable.php?name=day&date='+this.dateDay)
        .then((res) => {
          this.insert = res.data.newInsert;
          this.complete = res.data.newComplete;
          console.log(this.insert)
        }).catch(function (error) { // 请求失败处理
        console.log(error);
      });
    }
  },
  watch: {
    insert() {
      this.createCountMap()
    },
  },
  mounted() {
    this.getServerDate();
  }
}
</script>

<style scoped>

</style>
