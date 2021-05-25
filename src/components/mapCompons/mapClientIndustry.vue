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
  name: "mapClientIndustry",
  data(){
    return {
      addressValue:[],
      isTheme:false,
      theme:'',
    }
  },
  methods: {
    mapDark(sw){
      console.log(sw)
      this.isTheme = sw;
      this.isTheme ? this.theme = 'dark' : this.theme = ''
      this.createCountMap()
    },
    createCountMap() {
      // 销毁s
      if ( myChart != null && myChart !== "" && myChart !== undefined ) {
        myChart.dispose();
      }

      // 基于准备好的dom，初始化echarts实例
      myChart = echarts.init(this.$refs.main,this.theme);

      // 指定图表的配置项和数据
      let option = {
        title: {
          text: '客户行业分布图',
          left: 'center'
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b} : {c} ({d}%)'
        },
        legend: {
          left: 'center',
          top: 'bottom',
          data: ['IT互联网', '金融保险', '房地产建筑', '教育培训', '娱乐传媒', '医疗健康', '法律咨询', '其他行业']
        },
        series: [
          {
            name: '客户行业',
            type: 'pie',
            radius: [20, 140],
            center: ['50%', '50%'],
            roseType: 'radius',
            itemStyle: {
              borderRadius: 5
            },
            data: [
              {value: Number(this.addressValue[1]), name: 'IT互联网'},
              {value: Number(this.addressValue[2]), name: '金融保险'},
              {value: Number(this.addressValue[3]), name: '房地产建筑'},
              {value: Number(this.addressValue[4]), name: '教育培训'},
              {value: Number(this.addressValue[5]), name: '娱乐传媒'},
              {value: Number(this.addressValue[6]), name: '医疗健康'},
              {value: Number(this.addressValue[7]), name: '法律咨询'},
              {value: Number(this.addressValue[0]), name: '其他行业'}
            ]
          }
        ]
      };


      // 使用刚指定的配置项和数据显示图表。
      option && myChart.setOption(option)
    }
  },
  watch:{
    addressValue(){
      this.createCountMap()
    }
  },
  beforeCreate() {
    axios
      .get('./php/select/select_mapData.php?name=industry')
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
