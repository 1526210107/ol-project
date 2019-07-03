<template>
  <div class="box1">
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import Tile from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import { defaults as defaultControls, Attribution } from "ol/control";

export default {
  data() {
    return {
      map: null,
      attribution: null
    };
  },
  methods: {
    initMap() {
      let attribution = new Attribution({
        collapsible: false,   // 设置默认情况不能折叠 
        tipLabel: "我的"
      });
      this.attribution = attribution;

      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new OSM()
          })
        ],
        view: new View({
          center: [0, 0],
          zoom: 2
        }),
        logo: {src: 'http://weilin.me/ol3-primer/img/face_monkey.png', href: 'http://www.openstreetmap.org/'}, 
        // http://weilin.me/ol3-primer/img/face_monkey.png
        controls: defaultControls({attribution: false}).extend([attribution])
      });
    },

    // 窗口大小改变时，执行的函数
    handleResize() {
      let flag = this.map.getSize()[0] < 600;   // map.getSize()方法：获取当前地图的大小，返回值时DOM中地图对象的像素大小
      this.attribution.setCollapsible(flag);
      this.attribution.setCollapsed(flag);
    }

  },
  created(){
    window.addEventListener( 'resize',  this.handleResize );
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
