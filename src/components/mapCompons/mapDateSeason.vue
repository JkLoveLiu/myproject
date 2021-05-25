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
        v-model="dateSeason"
        type="year"
        size="small"
        value-format="yyyy"
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
  TitleComponent,
  ToolboxComponent,
  TooltipComponent,
  GridComponent,
  LegendComponent,
  MarkLineComponent,
  MarkPointComponent
} from 'echarts/components';
import {
  BarChart
} from 'echarts/charts';
import {
  CanvasRenderer
} from 'echarts/renderers';
import axios from "axios";

echarts.use(
  [TitleComponent, ToolboxComponent, TooltipComponent, GridComponent, LegendComponent, MarkLineComponent, MarkPointComponent, BarChart, CanvasRenderer]
);
let myChart;
export default {
  name: "mapDateSeason",
  data() {
    return {
      dateValue: [],
      isTheme: false,
      theme: '',
      dateSeason: '',
      insert: [],
      complete: [],
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
        title: {
          text: '年报表',
        },
        tooltip: {
          trigger: 'axis'
        },
        legend: {
          data: ['新增客户', '新增完成客户']
        },
        calculable: true,
        xAxis: [
          {
            type: 'category',
            data: ['第一季度', '第二季度', '第三季度', '第四季度']
          }
        ],
        yAxis: [
          {
            type: 'value'
          }
        ],
        series: [
          {
            name: '新增客户',
            type: 'bar',
            data: this.insert,
          },
          {
            name: '新增完成客户',
            type: 'bar',
            data: this.complete,
          }
        ]
      };
      option && myChart.setOption(option);
    },
    getServerDate() {
      axios
        .get('./php/select/select_mapDateTable.php?name=season&date=' + this.dateSeason)
        .then((res) => {
          this.insert = res.data.newInsert;
          this.complete = res.data.newComplete;
          console.log(res.data)
        }).catch(function (error) { // 请求失败处理
        console.log(error);
      });
    }
  },
  watch: {
    insert() {
      this.createCountMap()
    }
  },
  mounted() {
    this.getServerDate();
  }
}
</script>

<style scoped>

</style>
