<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import { Tile, Vector as VectorLayer, Heatmap } from "ol/layer";
import { OSM, Stamen, Vector as VectorSource } from "ol/source";
import { GeoJSON } from "ol/format";

export default {
  data() {
    return {
      map: null,

      //   模拟的数据列表
      testData: {
        max: 5,
        data: [
          { name: "乌鲁木齐", lat: 43.782225, lon: 87.576079, count: 1 },
          { name: "拉萨", lat: 29.71056, lon: 91.163218, count: 1 },
          { name: "西宁", lat: 36.593725, lon: 101.797439, count: 1 },
          { name: "兰州", lat: 36.119175, lon: 103.584421, count: 2 },
          { name: "成都", lat: 30.714315, lon: 104.035634, count: 3 },
          { name: "重庆", lat: 29.479073, lon: 106.519225, count: 4 },
          { name: "贵阳", lat: 26.457486, lon: 106.668183, count: 2 },
          { name: "昆明", lat: 24.969568, lon: 102.726915, count: 2 },
          { name: "银川", lat: 38.598593, lon: 106.167324, count: 2 },
          { name: "西安", lat: 34.276221, lon: 108.967213, count: 3 },
          { name: "南宁", lat: 22.748502, lon: 108.234036, count: 3 },
          { name: "海口", lat: 19.97015, lon: 110.346274, count: 3 },
          { name: "广州", lat: 23.183277, lon: 113.226755, count: 4 },
          { name: "长沙", lat: 28.170082, lon: 112.947996, count: 4 },
          { name: "南昌", lat: 28.652529, lon: 115.893762, count: 4 },
          { name: "福州", lat: 26.070956, lon: 119.246798, count: 4 },
          { name: "台北", lat: 25.008476, lon: 121.503585, count: 2 },
          { name: "杭州", lat: 30.330742, lon: 120.183062, count: 4 },
          { name: "上海", lat: 31.253514, lon: 121.449713, count: 5 },
          { name: "武汉", lat: 30.579401, lon: 114.216652, count: 5 },
          { name: "合肥", lat: 31.838495, lon: 117.262334, count: 3 },
          { name: "南京", lat: 32.085164, lon: 118.805714, count: 4 },
          { name: "郑州", lat: 34.746419, lon: 113.651151, count: 4 },
          { name: "济南", lat: 36.608511, lon: 117.048354, count: 4 },
          { name: "石家庄", lat: 38.033361, lon: 114.478253, count: 4 },
          { name: "太原", lat: 37.798488, lon: 112.483119, count: 3 },
          { name: "呼和浩特", lat: 40.895807, lon: 111.842856, count: 3 },
          { name: "天津", lat: 38.925801, lon: 117.351108, count: 4 },
          { name: "沈阳", lat: 41.801674, lon: 123.29626, count: 3 },
          { name: "长春", lat: 43.982041, lon: 125.261357, count: 4 },
          { name: "哈尔滨", lat: 45.693857, lon: 126.567056, count: 3 },
          { name: "北京", lat: 39.892297, lon: 116.068297, count: 5 },
          { name: "香港", lat: 22.428066, lon: 114.093184, count: 2 },
          { name: "澳门", lat: 22.18471, lon: 113.552554, count: 1 }
        ]
      }
    };
  },
  methods: {
    initMap() {

        // 处理数据，转为需要的数据格式
      let arr = [];
      this.testData.data.forEach(item => {
        let tempOjb = {};
        tempOjb.type = "Feature";
        tempOjb.properties = {};
        tempOjb.geometry = {
          type: "Point",
          coordinates: [item.lon, item.lat]
        };
        arr.push(tempOjb);
      });

      // 数据格式
      var heatData = {
        type: "FeatureCollection",
        features: [
          {
            type: "Feature",
            properties: {},
            geometry: { type: "Point", coordinates: [-2.9667, 56.466702] }
          },
          ...arr
        ]
      };

      // 创建矢量图层的数据源
      let vectorSource = new VectorSource({
        features: new GeoJSON().readFeatures(heatData, {
          dataProjection: "EPSG:4326",
          featureProjection: "EPSG:3857"
        })
      });

      // 创建矢量热力图层
      let vectorLayer = new Heatmap({
        source: vectorSource
      });

      //   地图的配置选项
      let options = {
        target: "mymap",
        layers: [
          new Tile({
            source: new Stamen({
              layer: "toner"
            })
          }),
          vectorLayer
        ],
        view: new View({
          center: [51933151, 4099470],
          zoom: 4
        })
      };

      //   初始化地图
      this.map = new Map(options);
    }

    /* 
        操作步骤：
            1. 构建基础图层的地图展示
            2. 创建Heatmap矢量图层
            3. 给矢量图层的数据源中添加要素，
    */
  },

  mounted() {
    this.initMap();
  }
};
</script>

<style scoped>
.box1 {
  flex-grow: 1;
}
#mymap {
  width: 100%;
  height: 500px;
}
</style>
