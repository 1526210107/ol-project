<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import Tile from "ol/layer/Tile";
import { OSM, TileArcGISRest } from "ol/source";

export default {
  data() {
    return {
      map: null
    };
  },
  methods: {
    initMap() {
      var url =
        "https://sampleserver1.arcgisonline.com/ArcGIS/rest/services/" +
        "Specialty/ESRI_StateCityHighway_USA/MapServer";

      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new OSM()
          }),
          new Tile({
            extent: [-13884991, 2870341, -7455066, 6338219],   // 表示此图层只会渲染extend指定的范围区域
            source: new TileArcGISRest({
              url: url
            })
          })
        ],
        view: new View({
          center: [-10997148, 4569099],
          zoom: 4
        })
      });
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
</style>
