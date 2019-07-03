<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { OSM, Vector as VectorSource, TileJSON } from "ol/source";
import {
  defaults as defaultInteractions,
  Pointer as PointerInteraction
} from "ol/interaction";
import { createRegularPolygon, createBox } from "ol/interaction/Draw";
import { defaults as defaultControls } from "ol/control";
import Feature from "ol/Feature";
import { LineString, Point, Polygon } from "ol/geom";
import { Fill, Icon, Stroke, Style } from "ol/style";

export default {
  data() {
    return {
      map: null
    };
  },
  methods: {
    initMap() {
      // 创建点要素
      var pointFeature = new Feature(new Point([0, 0]));

      // 创建线要素
      var lineFeature = new Feature(new LineString([[-1e7, 1e6], [-1e6, 3e6]]));

      // 创建多边形要素
      var polygonFeature = new Feature(
        new Polygon([
          [[-3e6, -1e6], [-3e6, 1e6], [-1e6, 1e6], [-1e6, -1e6], [-3e6, -1e6]]
        ])
      );

      this.map = new Map({
        target: "mymap",
        layers: [
          // 1. 瓦片图层
          new Tile({
            source: new TileJSON({
              url:
                "https://api.tiles.mapbox.com/v3/mapbox.geography-class.json?secure"
            })
          }),
          //   2. 矢量图层
          new VectorLayer({
            source: new VectorSource({
              features: [pointFeature, lineFeature, polygonFeature]
            }),
            style: new Style({
              image: new Icon({
                anchor: [0.5, 46],
                anchorXUnits: "fraction",
                anchorYUnits: "pixels",
                opacity: 0.95,
                src: require("../assets/icon.png")
              }),
              stroke: new Stroke({
                width: 3,
                color: [255, 0, 0, 1]
              }),
              fill: new Fill({
                color: [0, 0, 255, 0.6]
              })
            })
          })
        ],
        view: new View({
          center: [0, 0],
          zoom: 2
        }),

        // 地图及相应图形显示出来后，开始添加交互行为
        // interactions: defaultInteractions().extend([new Drag()])
      });
    },

    /**
     * @constructor
     * @extends {module:ol/interaction/Pointer}
     */
    Drag: (function(PointerInteraction) {
      function Drag() {
        PointerInteraction.call(this, {
          handleDownEvent: handleDownEvent,
          handleDragEvent: handleDragEvent,
          handleMoveEvent: handleMoveEvent,
          handleUpEvent: handleUpEvent
        });

        this.coordinate_ = null;
        this.cursor_ = "pointer";
        this.feature_ = null;
        this.previousCursor_ = undefined;
      }

      if (PointerInteraction) Drag.__proto__ = PointerInteraction;
      Drag.prototype = Object.create(
        PointerInteraction && PointerInteraction.prototype
      );
      Drag.prototype.constructor = Drag;
      return Drag;
    })(PointerInteraction)

  },

  mounted() {
    this.initMap();
  }

  /* 
        注意点：
            1. 在设置样式时，new Icon中的中的src取值问题，如果路径是本地图片，使用普通路径的方式，图片加载不出来，那么使用 require 引入即可。   

    */
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
