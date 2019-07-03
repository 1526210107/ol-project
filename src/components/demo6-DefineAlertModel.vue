<template>
  <div class="box1">
    <div id="mymap"></div>

    <!-- 地图上的弹窗层 -->
    <div id="popup" ref="container" class="ol-popup">
      <div id="popup-content" ref="content"></div>
    </div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View, Feature } from "ol";
import { toSize } from "ol/size";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { OSM, TileWMS, Vector as VectorSource } from "ol/source";
import { get as getProj } from "ol/proj";
import { Point } from "ol/geom";
import { Style, Icon } from "ol/style";
import Overlay from "ol/Overlay";

export default {
  data() {
    return {
      map: null
    };
  },
  methods: {
    initMap() {
      // 创建标记的样式
      const iconStyle = new Style({
        image: new Icon({
          opacity: 0.9,
          src:
            "https://ss0.bdstatic.com/94oJfD_bAAcT8t7mm9GUKT-xh_/timg?image&quality=100&size=b4000_4000&sec=1556889114&di=46950a2d626cb9999e6acda65686c0ea&src=http://pic.51yuansu.com/pic3/cover/01/59/31/594d771606856_610.jpg",
          // size: toSize([100, 100]),
          scale: 0.1
          // imgSize: [50, 50]
        })
      });

      // 创建矢量图层的要素Feature, 也就是标记的形状
      const iconFeature = new Feature({
        geometry: new Point([13276805.940731, 3008561.497087], "XY"),
        name: "my Icon"
      });

      // 创建矢量图层的数据源
      const vectorSource = new VectorSource({
        features: [iconFeature]
      });

      // 创建矢量图层
      const vectorLayer = new VectorLayer({
        source: vectorSource,
        style: iconStyle
      });

      // 创建覆盖物层
      let overlayLayer = new Overlay({
        position: [13276805.940731, 3008561.497087],
        element: this.$refs.container,
        autoPan: true
      });

      // 获取投影坐标系
      const pos = getProj("EPSG:3857");

      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new OSM()
          }),
          vectorLayer
        ],

        view: new View({
          projection: pos,
          center: [13276805.940731, 3008561.497087],
          zoom: 19
        })
      });

      this.map.on("click", e => {
        let _this = this;
        let pixel = e.pixel;
        _this.map.forEachFeatureAtPixel(pixel, function(feature) {
          //coodinate存放了点击时的坐标信息
          var coodinate = e.coordinate;
          //设置弹出框内容，可以HTML自定义
          _this.$refs.content.innerHTML = "<p>你点击的坐标为：" + coodinate + "</p>";
          //设置overlay的显示位置
          overlayLayer.setPosition(coodinate);
          //显示overlay
          _this.map.addOverlay(overlayLayer);
        });
      });

      // 打印当前地图使用的投影类型
      // console.log( this.map.getView().projection_.code_ );

      // 瓦片地图请求的链接
      // https://b.tile.openstreetmap.org/19/435841/222784.png
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

#popup {
  width: 300px;
  padding: 20px;
  background-color: #efedec;
}
</style>
