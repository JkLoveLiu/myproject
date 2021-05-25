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
  name: "mapClientAddress",
  data(){
    return {
      addressValue:[],
      isTheme:false,
      theme:'',
    }
  },
  methods: {
    mapDark(sw){
      this.isTheme = sw;
      this.isTheme ? this.theme = 'dark' : this.theme = ''
      this.createCountMap()
    },
    createCountMap() {
      if ( myChart != null && myChart !== "" && myChart !== undefined ) {
        myChart.dispose();
      }

      // 基于准备好的dom，初始化echarts实例
      myChart = echarts.init(this.$refs.main,this.theme);

      // 指定图表的配置项和数据
      let option = {
        title: {
          text: '客户地区分布图',
          left: 'center'
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b} : {c} ({d}%)'
        },
        legend: {
          left: 'center',
          top: 'bottom',
          data: ['华北地区', '东北地区', '华东地区', '华中地区', '华南地区', '西南地区', '西北地区', '港澳台']
        },
        series: [
          {
            name: '客户地区',
            type: 'pie',
            radius: [20, 140],
            center: ['50%', '50%'],
            roseType: 'area',
            itemStyle: {
              borderRadius: 5
            },
            data: [
              {value: Number(this.addressValue[1]), name: '华北地区'},
              {value: Number(this.addressValue[2]), name: '东北地区'},
              {value: Number(this.addressValue[3]), name: '华东地区'},
              {value: Number(this.addressValue[4]), name: '华中地区'},
              {value: Number(this.addressValue[5]), name: '华南地区'},
              {value: Number(this.addressValue[6]), name: '西南地区'},
              {value: Number(this.addressValue[7]), name: '西北地区'},
              {value: Number(this.addressValue[0]), name: '港澳台'}
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
      .get('./php/select/select_mapData.php?name=address')
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
