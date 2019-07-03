<template>
  <div class="box1">
    <div id="mymap"></div>
    <button @click="changePosition">点击切换位置</button>
    <!-- <img id="anchor" src="http://weilin.me/ol3-primer/img/anchor.png" ref="testImg" alt="demo"> -->
    <div id="anchor" style="width: 64px;height: 64px;" ref="testImg">
      <img id="anchorImg" src="http://weilin.me/ol3-primer/img/anchor.png" alt="示例锚点">
    </div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import Tile from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import Overlay from "ol/Overlay";

export default {
  data() {
    return {
      map: null,
      overlayLayer: null
    };
  },
  methods: {
    //   点击按钮时，改变位置
    changePosition() {
      this.overlayLayer.setPosition([50.57374, 10.51035]);
    },

    initMap() {
      const ele = this.$refs.testImg;

      // 创建覆盖物层
      this.overlayLayer = new Overlay({
        position: [102.57374, 30.51035],
        element: ele,
        autoPan: true
      });

      // 监听position的改变
      this.overlayLayer.on("change:position", function() {
        console.log("位置改变了");
      });

      this.map = new Map({
        target: "mymap",

        layers: [
          new Tile({
            source: new OSM()
          })
        ],

        overlays: [this.overlayLayer],

        view: new View({
          center: [0, 0],
          projection: "EPSG:4326",
          zoom: 2
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

@keyframes zoom {
  from {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
  50% {
    top: -16px;
    left: -16px;
    width: 64px;
    height: 64px;
  }
  to {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
}

@-moz-keyframes zoom /* Firefox */ {
  from {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
  50% {
    top: -16px;
    left: -16px;
    width: 64px;
    height: 64px;
  }
  to {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
}

@-webkit-keyframes zoom /* Safari 和 Chrome */ {
  from {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
  50% {
    top: -16px;
    left: -16px;
    width: 64px;
    height: 64px;
  }
  to {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
}

@-o-keyframes zoom /* Opera */ {
  from {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
  50% {
    top: -16px;
    left: -16px;
    width: 64px;
    height: 64px;
  }
  to {
    top: 0;
    left: 0;
    width: 32px;
    height: 32px;
  }
}

/* 应用css动画到图标元素上 */
#anchorImg {
  display: block;
  position: absolute;
  animation: zoom 5s;
  animation-iteration-count: infinite; /* 一直重复动画 */
  -moz-animation: zoom 5s; /* Firefox */
  -moz-animation-iteration-count: infinite; /* 一直重复动画 */
  -webkit-animation: zoom 5s; /* Safari 和 Chrome */
  -webkit-animation-iteration-count: infinite; /* 一直重复动画 */
  -o-animation: zoom 5s; /* Opera */
  -o-animation-iteration-count: infinite; /* 一直重复动画 */
}
</style>
