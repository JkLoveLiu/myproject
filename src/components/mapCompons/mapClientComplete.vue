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
    </div>
    <div ref="main" id="main" style="width: 500px;height:500px;"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import axios from "axios";

let myChart;
export default {
  name: "mapClientComplete",
  data() {
    return {
      addressValue: [],
      isTheme: false,
      theme: '',
    }
  },
  methods: {
    mapDark(sw) {
      console.log(sw)
      this.isTheme = sw;
      this.isTheme ? this.theme = 'dark' : this.theme = ''
      this.createCountMap()
    },
    createCountMap() {
      // 销毁
      if (myChart != null && myChart !== "" && myChart !== undefined) {
        myChart.dispose();
      }
      // 基于准备好的dom，初始化echarts实例
      myChart = echarts.init(this.$refs.main, this.theme);
      // 指定图表的配置项和数据
      let option = {
        tooltip: {
          trigger: 'item'
        },
        legend: {
          top: '5%',
          left: 'center'
        },
        series: [
          {
            name: '访问来源',
            type: 'pie',
            radius: ['40%', '70%'],
            avoidLabelOverlap: false,
            itemStyle: {
              borderRadius: 10,
              borderColor: '#fff',
              borderWidth: 2
            },
            label: {
              show: false,
              position: 'center'
            },
            emphasis: {
              label: {
                show: true,
                fontSize: '40',
                fontWeight: 'bold'
              }
            },
            labelLine: {
              show: false
            },
            data: [
              {value: this.addressValue[0], name: '已完成'},
              {value: this.addressValue[1], name: '未完成'},
            ]
          }
        ]
      };
      // 使用刚指定的配置项和数据显示图表。
      option && myChart.setOption(option)
    }
  },
  watch: {
    addressValue() {
      this.createCountMap()
    }
  },
  beforeCreate() {
    axios
      .get('./php/select/select_mapData.php?name=complete')
      .then((res) => {
        this.addressValue = res.data
      })
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
  }

}
</script>

<style scoped>

</style>
