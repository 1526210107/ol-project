<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View, Feature } from "ol";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { OSM, XYZ, Vector as VectorSource } from "ol/source";
import { transform, get as getProjection } from "ol/proj";
import { Style, Fill, Stroke, Circle, Icon } from "ol/style";
import { Point } from "ol/geom";
import Overlay from "ol/Overlay";
export default {
  data() {
    return {
      map: null,
      myoverlay: null,
      dataArr: [
        {
          lon: 100,
          lat: 20,
          name: "aaa"
        },
        {
          lon: 90,
          lat: 23,
          name: "bbb"
        },
        {
          lon: 40,
          lat: 50,
          name: "ccc"
        },
        {
          lon: 88,
          lat: 45,
          name: "ccc"
        },
        {
          lon: 110,
          lat: 35,
          name: "ccc"
        },
        {
          lon: 140,
          lat: 15,
          name: "ccc"
        }
      ]
    };
  },
  methods: {
    // 初始化地图的函数
    initMap() {
      let projection1 = getProjection("EPSG:4326");
      var projectionExtent1 = projection1.getExtent();
      console.log( projectionExtent1 );

      let projection2 = getProjection("EPSG:3857");
      var projectionExtent2 = projection2.getExtent();
      console.log( projectionExtent2 );


      let tileLayer = new Tile({
        source: new OSM({
          wrapX: false
        })
      });
      this.map = new Map({
        target: "mymap",
        layers: [tileLayer],
        view: new View({
          projection: "EPSG:3857",
          center: transform([130, 35], "EPSG:4326", "EPSG:3857"),
          // center: [0, 0],
          zoom: 3
        })
      });

      this.addVectorLayer();
    },

    // 给地图添加矢量图层
    addVectorLayer() {
      let vectorSource = new VectorSource({
        features: [],
        wrapX: false
      });
      let vectorLayer = new VectorLayer({
        source: vectorSource
      });
      // this.map.getLayers().push(vectorLayer);
      this.map.addLayer(vectorLayer);

      // 创建图标样式
      var iconStyle = new Style({
        image: new Icon({
          size: [50, 50],
          anchorXUnits: "pixels",
          anchorYUnits: "pixels",
          src: require("../assets/icon.png")
        })
      });

      // 创建要素，并添加到矢量图层上
      this.dataArr.forEach(item => {
        let feature = new Feature({
          geometry: new Point(
            transform([item.lon, item.lat], "EPSG:4326", "EPSG:3857")
          ),
          name: item.name,
          lon: item.lon,
          lat: item.lat
        });

        feature.setStyle(iconStyle);
        vectorSource.addFeature(feature);
      });

      // 地图绑定点击事件
      this.map.on("click", this.clickMapHandler);
    },

    // 地图被点击时的处理函数
    clickMapHandler(event) {
      let __this = this;
      // var pixel = this.map.getEventPixel(event.originalEvent);
      var pixel = event.pixel;
      this.map.forEachFeatureAtPixel(pixel, function(feature, layer) {
        if (feature != undefined) {
          console.log( feature );
          __this.map.removeOverlay( __this.myoverlay );
          let coordinate = feature.getGeometry().getCoordinates();
          __this.doAddOverlay( coordinate );
        }
      });
    },

    // 地图添加覆盖物的函数
    // [130, 35]
    doAddOverlay( coordinate = [4452779.631730943, 6446275.841017158] ) {
      let ele = this.createElem();
      let myoverlay = new Overlay({
        // position: transform( coordinate, "EPSG:4326", "EPSG:3857"),
        position: coordinate,
        element: ele,
        autoPan: true,
        offset: [30, 20]
      });
      this.map.addOverlay( myoverlay );
      this.myoverlay = myoverlay;
    },

    // 创建提示框的函数
    createElem(){
      let infoBox = document.createElement('div');
      infoBox.className = 'infobox';
      infoBox.innerHTML = '这是点击要素时，展示所点击要素信息的盒子';
      return infoBox;
    }

  },
  mounted() {
    this.initMap();
  }
};
</script>

<style>
.infobox{
  padding: 10px;
  width: 200px;
  background:  rgba(245,245,245,1);
  font-size: 12px;
}
</style>
