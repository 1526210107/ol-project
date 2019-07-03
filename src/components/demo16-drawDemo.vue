<template>
  <div class="box1">
    <div id="map"></div>
  </div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import VectorLayer from "ol/layer/Vector";
import OSM from "ol/source/OSM";
import VectorSource from "ol/source/Vector";
import { defaults } from "ol/control";
import Draw from "ol/interaction/Draw";
import Overlay from "ol/Overlay";
import { toStringHDMS } from "ol/coordinate";
import { transform } from "ol/proj";

export default {
  name: "MainDiv",
  data() {
    return {};
  },
  mounted: function() {

    var raster = new TileLayer({
      source: new OSM()
    });
    var source = new VectorSource({ wrapX: false });

    //ol.layer.Vector用于显示在客户端渲染的矢量数据。
    var vector = new VectorLayer({
      source: source
    });

    //地图部分
    var map = new Map({
      layers: [raster, vector],
      target: "map",
      view: new View({
        projection: "EPSG:4326",
        center: [120, 30],
        zoom: 4
      }),
      controls: defaults({
        attributionOptions: {
          collapsible: false
        }
      })
    });
  }
};
</script>


<style scoped>
.box1 {
  flex-grow: 1;
}
#map {
  width: 100%;
  height: 500px;
}
</style>