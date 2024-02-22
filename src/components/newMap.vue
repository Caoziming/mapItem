<template>
    <div class="plus">
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
const options = ref([]);
const key = 'e9eb835b494b4e20e7968e7bc9eae8c3';

const mapInstance = ref(null);
const markers = ref([]);

const onSearch = () => {
    fetch(`https://restapi.amap.com/v3/place/text?parameters&key=${key}&keywords=${keywords.value}`)
        .then((res) => res.json())
        .then((res) => {
            if (res.pois) {
                options.value = res.pois;
                console.log(options.value);
            }
        });
};

const onChangeLocation = (value) => {
    console.log(value);
    onMark(value);
};

const onMark = (location) => {
    mapInstance.value.remove(markers.value);
    markers.value = [];

    const temp = location.split(',');
    const marker = new AMap.Marker({
        map: mapInstance.value,
        position: [temp[0], temp[1]],
        // icon: '//a.amap.com/jsapi_demos/static/demo-center/icons/poi-marker-default.png',
        icon: 'data:image/svg+xml;base64,PHN2ZyB0PSIxNzA4NTIwNjA4OTA4IiBjbGFzcz0iaWNvbiIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgdmVyc2lvbj0iMS4xIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHAtaWQ9IjM3NTMiIHdpZHRoPSIxOTYiIGhlaWdodD0iMTk2Ij48cGF0aCBkPSJNOTE1LjEgNDYzLjljMC4xIDgxLjgtMjQuMiAxNTcuOS02Ni4xIDIyMS40LTU1LjMgODMuNy0yMjQuNCAyMTUuMi0yOTguNSAyNzAuOC0yMSAxNS43LTQ5LjggMTUuNy03MC44LTAuMS03Mi41LTU0LjUtMjM1LjgtMTgxLjUtMjkxLjUtMjYwLjQtNDYuMS02NS4zLTczLjEtMTQ0LjktNzMuMS0yMzAuOSAwLTIyMS4yIDE3OS4xLTQwMC42IDQwMC00MDAuNiAyMjEuMi0wLjEgMzk5LjYgMTc4LjMgNDAwIDM5OS44eiIgZmlsbD0iI0ZGNTU2RSIgcC1pZD0iMzc1NCI+PC9wYXRoPjxwYXRoIGQ9Ik00MTMuMSA5MDQuN2MyNS43IDIwLjMgNDguOSAzNy45IDY2LjYgNTEuMyAyMSAxNS44IDQ5LjggMTUuOCA3MC44IDAuMSAyMi4xLTE2LjYgNTIuNy0zOS45IDg2LjItNjYuNy00My41IDE1LjUtOTAuNCAyNC0xMzkuMiAyNC0yOS0wLjEtNTcuMi0zLTg0LjQtOC43ek01MTUuMSA2NGMtMTA2LjMgMC0yMDMgNDEuNi0yNzQuNiAxMDkuMyA3MC42LTU1LjkgMTU5LjktODkuMiAyNTctODkuMiAyMjkgMCA0MTQuNiAxODUuNiA0MTQuNiA0MTQuNiAwIDYuNC0wLjIgMTIuOC0wLjQgMTkuMSAyLjMtMTcuNiAzLjUtMzUuNiAzLjUtNTMuOUM5MTQuNyAyNDIuNCA3MzYuMyA2NCA1MTUuMSA2NHoiIGZpbGw9IiNGRjU1NkUiIHAtaWQ9IjM3NTUiPjwvcGF0aD48cGF0aCBkPSJNNDk3LjQgODQuMmMtOTcuMSAwLTE4Ni4zIDMzLjQtMjU3IDg5LjItNzcuMiA3My0xMjUuNCAxNzYuNS0xMjUuNCAyOTEuMiAwIDg2IDI3LjEgMTY1LjcgNzMuMSAyMzAuOSA0Mi4xIDU5LjYgMTQ1LjUgMTQ2LjYgMjI0LjkgMjA5LjIgMjcuMiA1LjYgNTUuNSA4LjYgODQuNCA4LjYgNDguOCAwIDk1LjctOC41IDEzOS4yLTI0IDc4LjctNjMgMTczLjUtMTQ1LjMgMjEyLjMtMjA0LjEgMzIuNS00OS4zIDU0LjUtMTA2LjIgNjIuNi0xNjcuNSAwLjMtNi4zIDAuNC0xMi43IDAuNC0xOS4xIDAuMS0yMjguOC0xODUuNS00MTQuNC00MTQuNS00MTQuNHpNNDc5LjggODM3QzI4Mi4yIDgzNyAxMjIgNjc2LjggMTIyIDQ3OS4yczE2MC4yLTM1Ny44IDM1Ny44LTM1Ny44IDM1Ny44IDE2MC4yIDM1Ny44IDM1Ny44UzY3Ny40IDgzNyA0NzkuOCA4Mzd6IiBmaWxsPSIjRkY1RjcxIiBwLWlkPSIzNzU2Ij48L3BhdGg+PHBhdGggZD0iTTQ3OS44IDEyMS41QzI4Mi4yIDEyMS41IDEyMiAyODEuNyAxMjIgNDc5LjJTMjgyLjIgODM3IDQ3OS44IDgzN3MzNTcuOC0xNjAuMiAzNTcuOC0zNTcuOC0xNjAuMi0zNTcuNy0zNTcuOC0zNTcuN3ogbS0xNy43IDYzOS4yYy0xNjYuMiAwLTMwMS0xMzQuNy0zMDEtMzAxczEzNC43LTMwMSAzMDEtMzAxIDMwMSAxMzQuNyAzMDEgMzAxLTEzNC43IDMwMS0zMDEgMzAxeiIgZmlsbD0iI0ZGNjk3NCIgcC1pZD0iMzc1NyI+PC9wYXRoPjxwYXRoIGQ9Ik00NjIuMSAxNTguOGMtMTY2LjIgMC0zMDEgMTM0LjctMzAxIDMwMXMxMzQuNyAzMDEgMzAxIDMwMSAzMDEtMTM0LjcgMzAxLTMwMS0xMzQuNy0zMDEtMzAxLTMwMXogbS0xNy42IDUyNS42Yy0xMzQuOCAwLTI0NC4yLTEwOS4zLTI0NC4yLTI0NC4yUzMwOS42IDE5NiA0NDQuNSAxOTZzMjQ0LjIgMTA5LjMgMjQ0LjIgMjQ0LjItMTA5LjQgMjQ0LjItMjQ0LjIgMjQ0LjJ6IiBmaWxsPSIjRkY3Mzc3IiBwLWlkPSIzNzU4Ij48L3BhdGg+PHBhdGggZD0iTTQ0NC41IDE5Ni4xYy0xMzQuOCAwLTI0NC4yIDEwOS4zLTI0NC4yIDI0NC4yczEwOS4zIDI0NC4yIDI0NC4yIDI0NC4yIDI0NC4yLTEwOS4zIDI0NC4yLTI0NC4yLTEwOS40LTI0NC4yLTI0NC4yLTI0NC4yeiBtLTE3LjcgNDEyYy0xMDMuNSAwLTE4Ny4zLTgzLjktMTg3LjMtMTg3LjNzODMuOS0xODcuNCAxODcuMy0xODcuNCAxODcuMyA4My45IDE4Ny4zIDE4Ny4zLTgzLjggMTg3LjQtMTg3LjMgMTg3LjR6IiBmaWxsPSIjRkY3RTdBIiBwLWlkPSIzNzU5Ij48L3BhdGg+PHBhdGggZD0iTTQyNi44IDIzMy40Yy0xMDMuNSAwLTE4Ny4zIDgzLjktMTg3LjMgMTg3LjNTMzIzLjQgNjA4IDQyNi44IDYwOHMxODcuMy04My45IDE4Ny4zLTE4Ny4zLTgzLjgtMTg3LjMtMTg3LjMtMTg3LjN6IG0tMTcuNiAyOTguNGMtNzIuMSAwLTEzMC41LTU4LjQtMTMwLjUtMTMwLjVzNTguNC0xMzAuNSAxMzAuNS0xMzAuNSAxMzAuNSA1OC40IDEzMC41IDEzMC41LTU4LjQgMTMwLjUtMTMwLjUgMTMwLjV6IiBmaWxsPSIjRkY4ODdEIiBwLWlkPSIzNzYwIj48L3BhdGg+PHBhdGggZD0iTTQwOS4yIDQwMS4zbS0xMzAuNSAwYTEzMC41IDEzMC41IDAgMSAwIDI2MSAwIDEzMC41IDEzMC41IDAgMSAwLTI2MSAwWiIgZmlsbD0iI0ZGOTI4MCIgcC1pZD0iMzc2MSI+PC9wYXRoPjxwYXRoIGQ9Ik01MTUuMSA4MmM1MS43IDAgMTAxLjcgMTAuMSAxNDguOCAyOS45IDQ1LjUgMTkuMiA4Ni4zIDQ2LjcgMTIxLjMgODEuN1M4NDcuOCAyNjkuNSA4NjcgMzE1YzE5LjkgNDcuMSAzMCA5Ny4yIDMwLjEgMTQ4LjkgMC4xIDc1LjYtMjEuNyAxNDguNy02My4xIDIxMS40LTU0IDgxLjgtMjIzLjggMjEzLjUtMjk0LjIgMjY2LjMtNy4xIDUuNC0xNS42IDguMi0yNC42IDguMnMtMTcuNS0yLjgtMjQuNy04LjJjLTY4LjMtNTEuMy0yMzMtMTc5LTI4Ny42LTI1Ni40LTQ1LjctNjQuNy02OS44LTE0MS02OS44LTIyMC42IDAtNTEuNyAxMC4xLTEwMS44IDMwLTE0OC45IDE5LjItNDUuNiA0Ni44LTg2LjUgODEuOS0xMjEuNnM3NS45LTYyLjcgMTIxLjQtODJDNDEzLjUgOTIuMiA0NjMuNSA4MiA1MTUuMSA4Mm0wLTE4Yy0yMjAuOSAwLTQwMCAxNzkuMy00MDAgNDAwLjYgMCA4NiAyNy4xIDE2NS43IDczLjEgMjMwLjkgNTUuNyA3OC45IDIxOSAyMDUuOSAyOTEuNSAyNjAuNCAxMC41IDcuOSAyMyAxMS45IDM1LjUgMTEuOSAxMi40IDAgMjQuOS0zLjkgMzUuMy0xMS44IDc0LjEtNTUuNSAyNDMuMi0xODcgMjk4LjUtMjcwLjcgNDEuOS02My41IDY2LjMtMTM5LjYgNjYuMS0yMjEuNEM5MTQuNyAyNDIuNCA3MzYuMyA2NCA1MTUuMSA2NHoiIGZpbGw9IiNFRjQ4NjgiIHAtaWQ9IjM3NjIiPjwvcGF0aD48cGF0aCBkPSJNNTE1LjEgMjcxLjVjMTA1LjcgMCAxOTEuMyA4NS42IDE5MS4zIDE5MS4zcy04NS43IDE5MS4zLTE5MS4zIDE5MS4zYy0xMDUuNyAwLTE5MS4zLTg1LjctMTkxLjMtMTkxLjNzODUuNi0xOTEuMyAxOTEuMy0xOTEuM3oiIGZpbGw9IiNGRkM3QzciIHAtaWQ9IjM3NjMiPjwvcGF0aD48cGF0aCBkPSJNMjU0LjcgMjY1LjRjLTQgMC04LjEtMS4zLTExLjQtNC4xLTcuNy02LjMtOC44LTE3LjYtMi41LTI1LjMgNDgtNTguNiAxMDQuNi04Ny44IDEwNi45LTg5IDguOS00LjUgMTkuNy0xIDI0LjIgNy45IDQuNSA4LjggMSAxOS43LTcuOCAyNC4yLTAuNSAwLjMtNTIuNSAyNy4zLTk1LjUgNzkuOC0zLjYgNC4yLTguNyA2LjUtMTMuOSA2LjV6TTIxNC40IDMyNC4yYy0zLjIgMC02LjUtMC45LTkuNC0yLjctOC41LTUuMi0xMS4xLTE2LjMtNS45LTI0LjhsNS45LTkuNmM1LjItOC41IDE2LjMtMTEuMSAyNC44LTUuOXMxMS4xIDE2LjMgNS45IDI0LjhsLTUuOSA5LjZjLTMuNCA1LjUtOS40IDguNi0xNS40IDguNnoiIGZpbGw9IiNGRkZGRkYiIHAtaWQ9IjM3NjQiPjwvcGF0aD48L3N2Zz4=',
        offset: new AMap.Pixel(-30, -40),
    });

    markers.value.push(marker);
    mapInstance.value.setFitView();
};

onMounted(() => {
    AMapLoader.load({
        key: "d2feeacab0796e8b796978d55343a518",
        version: "2.0",
        plugins: [],
    })
        .then((AMap) => {
            const map = new AMap.Map("container", {
                viewMode: "2D",
                zoom: 11,
                center: [113.264499, 23.130061],
            });
            map.on("click", (e) => {
                map.remove(markers.value);
                markers.value = [];
                const marker = new AMap.Marker({
                    map: map,
                    position: [e.lnglat.getLng(), e.lnglat.getLat()],
                    icon: '//a.amap.com/jsapi_demos/static/demo-center/icons/poi-marker-default.png',
                    offset: new AMap.Pixel(-30, -40),

                });
                markers.value.push(marker);
                map.setFitView();
            });
            mapInstance.value = map;
        })
        .catch((e) => {
            console.log(e);
        });
});

onUnmounted(() => {
    mapInstance.value?.destroy();
});
</script>
  
<style >
  #container {
    width: 800px;
    height: 800px;
    .amap-icon{
       overflow: unset !important;
       width: 100px;
       img{
        width:30px;
        height: 40px;
       }
   }
  }


  </style>
  