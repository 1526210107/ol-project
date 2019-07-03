<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View, Feature } from "ol";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { OSM, XYZ, Stamen, Vector as VectorSource } from "ol/source";
import { LineString } from "ol/geom";
import { Stroke, Style } from "ol/style";
export default {
  data() {
    return {
      map: null
    };
  },
  methods: {
    initMap() {
      // 初始化地图
      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new Stamen({
              layer: "toner"
            })
          })
        ],
        view: new View({
          center: [0, 0],
          zoom: 2
        })
      });

      // 创建样式
      var style = new Style({
        stroke: new Stroke({
          color: "#EAE911",
          width: 5
        })
      });

      // 创建飞行线的数据源
      var flightsSource = new VectorSource({
        wrapX: false,
        attributions:
          "Flight data by " +
          '<a href="http://openflights.org/data.html">OpenFlights</a>,',
        loader: function() {
          let url = "/flights.json";
          fetch(url)
            .then(function(response) {
              return response.json();
            })
            .then(function(json) {
              var flightsData = json.flights;
              for (var i = 0; i < flightsData.length; i++) {
                var flight = flightsData[i];
                var from = flight[0];
                var to = flight[1];

                // create an arc circle between the two locations
                var arcGenerator = new arc.GreatCircle(
                  { x: from[1], y: from[0] },
                  { x: to[1], y: to[0] }
                );

                var arcLine = arcGenerator.Arc(100, { offset: 10 });
                if (arcLine.geometries.length === 1) {
                  var line = new LineString(arcLine.geometries[0].coords);
                  line.transform("EPSG:4326", "EPSG:3857");

                  var feature = new Feature({
                    geometry: line,
                    finished: false
                  });
                  // add the feature with a delay so that the animation
                  // for all features does not start at the same time
                  flightsSource.addFeature(feature);
                }
              }
            });
        }
      });

      // 创建飞行线的图层
      var flightLayer = new VectorLayer({
        source: flightsSource,
        style: function(feature) {
          // 设置动画状态的特征不使用图层样式
          if (feature.get("finished")) {
            return style;
          } else {
            return null;
          }
        }
      });

      this.map.addLayer(flightLayer);
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
  height: 670px;
}
</style>
