<template>
  <div class="box1">
    <div id="mymap"></div>
    <div>
      <!-- 放置值的div -->
      <div ref="mousePosition"></div>

      <!-- 选择投影坐标系 -->
      <el-select v-model="projectionType" placeholder="请选择" @change="projectionHandle">
        <el-option value="EPSG:4326">EPSG:4326</el-option>
        <el-option value="EPSG:3857">EPSG:3857</el-option>
      </el-select>

      <!-- 选择精确度 -->
      <el-input v-model="exactValue" type="number" class="exact-box" @change="exactHandle"></el-input>
    </div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import Tile from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import {
  defaults as defaultControls,
  MousePosition,
  FullScreen,
  OverviewMap,
  Rotate,
  ScaleLine,
  ZoomSlider,
  ZoomToExtent
} from "ol/control";
import { createStringXY } from "ol/coordinate";

export default {
  data() {
    return {
      projectionType: "EPSG:4326",
      exactValue: 4,
      map: null,
      mousePositionControl: null
    };
  },
  methods: {
    overviewMapRenderFun() {
      console.log(" 重新绘制了 ");
    },
    initMap() {
      // 地图的全局地图控件: 显示当前窗口中的地图位于全局地图的哪一个部分。
      const overviewMap = new OverviewMap({
        // target: '', // 指定控件放置的位置
        collapsed: false // 默认是否折叠
        // collapsible: false,
        // collapseLabel: '11',  //展开后按钮的内容 text|HTMLElement
        // label: '22',   // 折叠时按钮的内容 text|HTMLElement
        // render: this.overviewMapRenderFun,   // 当overviewmapcontrol重新绘制时，执行的函数
      });

      // 地图的全屏控件
      const fullScreenControl = new FullScreen();

      // 地图的旋转控件
      const rotateControl = new Rotate({
        autoHide: false
      });

      // 地图的比例尺控件
      const scaleLineControl = new ScaleLine({
        // units: "degrees"
      });

      // 地图的缩放控件
      // const zoomControl = new Zoom();

      // 缩放滑块控件
      const zoomSliderControl = new ZoomSlider();

      // 按钮式缩放到特定范围的控件
      const zoomToExtentControl = new ZoomToExtent({
        extend: [13100000, 4290000, 13200000, 5210000]
      });

      // 获得鼠标位置控件
      this.mousePositionControl = new MousePosition({
        className: "custom-mouse-position",
        target: this.$refs.mousePosition, // 鼠标坐标显示在页面中的位置
        undefinedHTML: "&nbsp;", // 当鼠标离开地图时，显示的内容
        coordinateFormat: createStringXY(4), // createStringXY执行后返回CoordinateFormat对象, 它的参数4表示精确的位数
        projection: "EPSG:3857" // 投影类型
      });

      this.map = new Map({
        target: "mymap",

        controls: defaultControls().extend([
          this.mousePositionControl,
          fullScreenControl,
          overviewMap,
          rotateControl,
          scaleLineControl,
          zoomSliderControl,
          zoomToExtentControl
        ]),

        layers: [
          new Tile({
            source: new OSM()
          })
        ],

        view: new View({
          center: [0, 0],
          zoom: 2
        })
      });
    },

    // 选择不同投影类型时的处理函数
    projectionHandle() {
      this.mousePositionControl.setProjection(this.projectionType);
    },

    // 选择不同精度时的处理函数
    exactHandle() {
      let format = createStringXY(this.exactValue);
      this.mousePositionControl.setCoordinateFormat(format);
    }
  },

  mounted() {
    this.initMap();
  }
};

/* 
  鼠标位置的显示属于地图控件:
    1. 首先在默认的地图控件队列中，添加上鼠标控件mousePositionControl
    2. 创建对应的鼠标位置控件即可
*/
</script>

<style>
.box1 {
  flex-grow: 1;
}
#mymap {
  width: 100%;
  height: 500px;
}
.exact-box {
  width: 217px !important;
  margin-left: 20px;
}

.ol-rotate {
  right: 2.5rem !important;
}


.ol-scale-line {
  left: 190px;
}


</style>
