<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View, Feature } from "ol";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { OSM, Vector as VectorSource } from "ol/source";
import { Point } from "ol/geom";
import { Style, Circle, Fill } from "ol/style";

export default {
  data() {
    return {
      map: null,
      pointFeature: null,
      styleArr: []
    };
  },
  methods: {
    initMap() {
      let vectorLayer = new VectorLayer({
        source: new VectorSource({
          wrapX: false
        })
      });

      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new OSM({
              wrapX: false
            })
          }),
          vectorLayer
        ],
        view: new View({
          center: [0, 0],
          zoom: 4
        })
      });

      // 创建点要素
      let pointFeature = new Feature({
        geometry: new Point([0, 0])
      });
      this.pointFeature = pointFeature;

      //   创建样式数组
      let styleArr = [
        new Style({
          image: new Circle({
            radius: 100,
            fill: new Fill({
              color: "orange"
            })
          })
        }),
        new Style({
          image: new Circle({
            radius: 100,
            fill: new Fill({
              color: "green"
            })
          })
        })
      ];
      this.styleArr = styleArr;

      pointFeature.setStyle(styleArr[0]);

      vectorLayer.getSource().addFeature(pointFeature);

      this.addClick();
    },

    // 给地图添加点击事件的函数
    addClick() {
      let _this = this;

      this.map.on("pointermove", ev => {
        console.log(1);
        this.map.forEachFeatureAtPixel(ev.pixel, function(feature) {
          // 为移动到的feature发送自定义的mousemove消息
          feature.dispatchEvent({ type: "mousemove", event: event });
        });
        //   console.log( this.map.getLayers().array_[1].getSource().getFeatures()[0].setStyle( this.styleArr[1] ) )
      });

      this.pointFeature.on("mousemove", function() {
        this.setStyle(_this.styleArr[1]);
      });
    }
  },
  mounted() {
    this.initMap();
  }
};
</script>

<style scoped>
</style>
