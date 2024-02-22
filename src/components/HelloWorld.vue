<template>
  <div>
    <input type="text" v-model="keywords"><button @click="onSearch">搜索</button>
    <!-- <button @click="onMark">地图标点</button> -->
    <el-select v-model="value" class="m-2" placeholder="Select" size="large" style="width: 240px" @change="onChangeLocation">
      <el-option v-for="item in options" :key="item.location" :label="item.name" :value="item.location" />
    </el-select>
  </div>
  <div id="container"></div>
</template>
<script setup>
import { onMounted, onUnmounted, ref } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
const keywords = ref('');

// 存放搜索的 数据
const options = ref([]);

// 这个用的是web服务的key
const key = 'e9eb835b494b4e20e7968e7bc9eae8c3';
// 搜索
const onSearch = () => {
  console.log(keywords.value);
  fetch(`https://restapi.amap.com/v3/place/text?parameters&key=${key}&keywords=${keywords.value}`)
    .then((res) => res.json())
    .then((res) => {
      if (res.pois) {
        options.value = res.pois
        res.pois.forEach(item => {
          console.log(item)
          console.log(item.name, item.location);
        });
      }
    });
};

// 选择地点
const onChangeLocation = (value)=>{
  console.log(value);
  onMark(value)
}

// let map = null;
// let AMap = null;

// 地图标点
const onMark = (location) => {
  const temp = location.split(',');
  // map = window.map
  // AMap = window.AMap
  // 需要一个经纬度
  window.map.on("click", (e) => {
    //1. 清除所有的点
    window.map.remove(markers.value);
    markers.value = [];
    //2. 追加当前的点
    const marker = new window.AMap.Marker({
      map: window.map,
      // position: [e.lnglat.getLng(), e.lnglat.getLat()], // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
      position: [temp[0], temp[1]], // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
      icon: '//a.amap.com/jsapi_demos/static/demo-center/icons/poi-marker-default.png',
      offset: new window.AMap.Pixel(-13, -30),
    });
    console.log(marker);
    markers.value.push(marker);
    //3. 移动
    window.map.setFitView();
  });
};


// 存放所有的地图标记
const markers = ref([]);

onMounted(() => {
  AMapLoader.load({
    key: "d2feeacab0796e8b796978d55343a518", // 申请好的Web端开发者Key，首次调用 load 时必填
    version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
    plugins: [], // 需要使用的的插件列表，如比例尺'AMap.Scale'等
  })
    .then((AMap) => {

      const map = new AMap.Map("container", {
        // 设置地图容器id
        viewMode: "2D", // 是否为3D地图模式
        zoom: 11, // 初始化地图级别
        center: [113.264499, 23.130061], // 初始化地图中心点位置
      });

      // 功能1: 点击地图添加地图标记

      map.on("click", (e) => {
        //1. 清除所有的点
        map.remove(markers.value);
        markers.value = [];
        //2. 追加当前的点
        const marker = new AMap.Marker({
          map: map,
          position: [e.lnglat.getLng(), e.lnglat.getLat()], // 经纬度对象，也可以是经纬度构成的一维数组[116.39, 39.9]
          icon: '//a.amap.com/jsapi_demos/static/demo-center/icons/poi-marker-default.png',
          offset: new AMap.Pixel(-13, -30),
        });
        console.log(marker);
        markers.value.push(marker);
        //3. 移动
        map.setFitView();
      });
      window.map = map;
      window.AMap = AMap;

    })
    .catch((e) => {
      console.log(e);
    });
});

onUnmounted(() => {
  map?.destroy();
});
</script>



<style scoped>
#container {
  width: 800px;
  height: 800px;
}
</style>
