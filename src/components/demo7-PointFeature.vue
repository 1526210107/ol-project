<template>
  <div class="box1">
    <div id="mymap"></div>
    <div class="block">
      <span class="demonstration">集群距离</span>
      <el-slider v-model="distanceValue" @change="handleDistance"></el-slider>
    </div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { Cluster, OSM, Vector as VectorSource } from "ol/source";
import Feature from "ol/Feature";
import { Point } from "ol/geom";
import { Circle as CircleStyle, Fill, Stroke, Style, Text } from "ol/style";

export default {
  data() {
    return {
      distanceValue: 10,
      clusterSource: null,
      map: null
    };
  },
  methods: {
    initMap() {
      // 创建适量图层数据源的要素
      let count = 20000;
      let features = new Array(count);
      var e = 4500000;
      for (var i = 0; i < count; ++i) {
        let coordinates = [
          2 * e * Math.random() - e,
          2 * e * Math.random() - e
        ];
        features[i] = new Feature(new Point(coordinates));
      }

      // 创建适量图层数据源
      let vectorSource = new VectorSource({
        features: features
      });

      // 创建集群的数据源
      var clusterSource = new Cluster({
        distance: parseInt(this.distanceValue, 10),
        source: vectorSource
      });
      this.clusterSource = clusterSource;

      // 创建矢量图层
      let styleCache = {};
      let vectorLayer = new VectorLayer({
        source: clusterSource,
        style: function(feature) {
          var size = feature.get("features").length;
          let style = styleCache[size];
          if (!style) {
            style = new Style({
              image: new CircleStyle({
                radius: 10,
                stroke: new Stroke({
                  color: "#fff"
                }),
                fill: new Fill({
                  color: "#3399CC"
                })
              }),
              text: new Text({
                text: size.toString(),
                fill: new Fill({
                  color: "#fff"
                })
              })
            });
            styleCache[size] = style;
          }
          return style;
        }
      });

      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new OSM()
          }),
          vectorLayer
        ],
        view: new View({
          center: [0, 0],
          zoom: 2
        })
      });
    },

    // 滑块的处理函数
    handleDistance() {
      this.clusterSource.setDistance(parseInt(this.distanceValue, 10));
    }
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

.block {
  margin: 20px;
}
</style>
