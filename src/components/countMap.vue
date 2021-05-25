<template>
  <div class="templateWindow">
    <div class="treeCtrl">
      <el-menu :default-active="mapActiveIndex"
               class="el-menu-demo"
               mode="vertical"
               :unique-opened="true"
               @select="mapHandleSelect">
        <el-submenu index="1">
          <template slot="title">客户统计分析</template>
          <el-menu-item index="1-1">客户类型</el-menu-item>
          <el-menu-item index="1-2">客户意向</el-menu-item>
          <el-menu-item index="1-3">客户地区</el-menu-item>
          <el-menu-item index="1-4">客户行业</el-menu-item>
          <el-menu-item index="1-5">完成情况</el-menu-item>
        </el-submenu>
        <el-submenu index="2">
          <template slot="title">时间统计分析</template>
          <el-menu-item index="2-1">日报表</el-menu-item>
          <el-menu-item index="2-2">月报表</el-menu-item>
          <el-menu-item index="2-3">季报表</el-menu-item>
          <el-menu-item index="2-4">年报表</el-menu-item>
        </el-submenu>
      </el-menu>
    </div>
    <div class="mapCtrl">
<!--   客户分类   -->
      <map-client-type v-if="mapShow.type"></map-client-type>
      <map-client-complete v-if="mapShow.complete"></map-client-complete>
      <map-client-address v-if="mapShow.address"></map-client-address>
      <map-client-industry v-if="mapShow.industry"></map-client-industry>
      <map-client-statue v-if="mapShow.statue"></map-client-statue>
<!--   时间统计   -->
      <map-date-day v-if="mapShow.day"></map-date-day>
      <map-date-month v-if="mapShow.month"></map-date-month>
      <map-date-season v-if="mapShow.season"></map-date-season>
      <map-date-year v-if="mapShow.year"></map-date-year>
    </div>
  </div>
</template>

<script>
import MapClientAddress from "./mapCompons/mapClientAddress";
import MapClientIndustry from "./mapCompons/mapClientIndustry";
import MapClientComplete from "./mapCompons/mapClientComplete";
import MapClientType from "./mapCompons/mapClientType";
import MapClientStatue from "./mapCompons/mapClientStatue";
import MapDateMonth from "./mapCompons/mapDateMonth";
import MapDateDay from "./mapCompons/mapDateDay";
import MapDateYear from "./mapCompons/mapDateYear";
import MapDateSeason from "./mapCompons/mapDateSeason";

export default {
  name: "countMap",
  components: {
    MapDateSeason,
    MapDateYear,
    MapDateDay,
    MapDateMonth, MapClientStatue, MapClientType, MapClientComplete, MapClientIndustry, MapClientAddress},
  data() {
    return {
      mapTitle:'',
      mapData:[],
      mapActiveIndex: '1-1',
      mapShow:{
        type:true,
        statue:false,
        address:false,
        industry:false,
        complete:false,
        day:false,
        month:false,
        season:false,
        year:false
      }
    }
  },
  methods: {
    // 树形控件的方法
    mapHandleSelect(key, keyPath) {
      for (let mapShowKey in this.mapShow) {
        this.mapShow[mapShowKey] = false;
      }
      switch (key) {
        case '1-1':
          this.mapShow.type = true;
          break;
        case '1-2':
          this.mapShow.statue = true;
          break;
        case '1-3':
          this.mapShow.address = true;
          break;
        case '1-4':
          this.mapShow.industry = true;
          break;
        case '1-5':
          this.mapShow.complete = true;
          break;
        case '2-1':
          this.mapShow.day = true;
          break;
        case '2-2':
          this.mapShow.month = true;
          break;
        case '2-3':
          this.mapShow.season = true;
          break;
        case '2-4':
          this.mapShow.year = true;
          break;
      }
    },

  },
  mounted() {
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

.treeCtrl {
  width: 200px;
  height: 500px;
  background: white;
  float: left;
  margin-top: 15px;
  margin-left: 15px;
  font-size: 22px;
}
.treeCtrl button{
  margin-bottom: 10px;
}
.mapCtrl {
  float: left;
  position: absolute;
  left: 250px;
  top: 20px;
}
</style>
