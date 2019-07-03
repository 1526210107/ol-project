<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import { Tile } from "ol/layer";
import { OSM, XYZ } from "ol/source";

export default {
  data() {
    return {
      map: null
    };
  },
  methods: {
    initMap() {
      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new OSM()
          })
        ],
        view: new View({
          center: [0, 0],
          zoom: 3
        })
      });
    }
  },
  mounted() {
    this.initMap();
    /* 
      地图：
        target: 渲染地图到指定的元素id中
        layers: 地图包含的图层
        view: 地图的视图，可以设置中心点，投影和缩放级别等等
    */
  }
};
</script>

<style scoped>
.box1 {
  flex-grow: 1;
}
#mymap {
  width: 100%;
  height: 670px;
}
</style>
