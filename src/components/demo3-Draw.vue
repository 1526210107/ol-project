<template>
  <div class="box1">
    <el-row style="margin:20px;">
      <el-col :span="8">
        <el-select v-model="polygon" placeholder="请选择" @change="handleChange" style="width:100%">
          <el-option value="Point">Point</el-option>
          <el-option value="LineString">LineString</el-option>
          <el-option value="Polygon">Polygon</el-option>
          <el-option value="Circle">Circle</el-option>
          <el-option value="None">None</el-option>

          <!-- 下面的需要特殊处理，需要配置 geometryFunction 选项  -->
          <el-option value="Square">Square</el-option>
          <el-option value="Box">Box</el-option>
          <el-option value="Star">Star</el-option>
        </el-select>
      </el-col>
      <el-col :span="10" :offset="1">
        <div style="height:40px;line-height:40px;">
          <el-radio v-model="shallSmooth" :label="false">直线</el-radio>
          <el-radio v-model="shallSmooth" :label="true">弧线</el-radio>
        </div>
      </el-col>
    </el-row>

    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { OSM, Vector as VectorSource } from "ol/source";
import { Draw } from "ol/interaction";
import { createRegularPolygon, createBox } from "ol/interaction/Draw";
import smooth from "chaikin-smooth";

import Polygon from "ol/geom/Polygon";

export default {
  data() {
    return {
      polygon: "None",
      map: null,
      draw: null,
      vectorSource: null,
      shallSmooth: true // 控制绘制出来的线条是否平滑
    };
  },

  methods: {
    initMap() {
      this.vectorSource = new VectorSource({
        wrapX: false
      });

      // 创建矢量图层
      const vectorLayer = new VectorLayer({
        source: this.vectorSource
      });

      // 创建瓦片图层
      const TileLayer = new Tile({
        source: new OSM()
      });

      // 创建地图
      this.map = new Map({
        target: "mymap",
        layers: [TileLayer, vectorLayer],
        view: new View({
          center: [0, 0],
          zoom: 5
        })
      });

      // 给地图添加交互行为
      this.addInteractionMy();
    },

    // 给地图添加交互行为
    addInteractionMy() {
      if (this.polygon !== "None") {
        var geometryFunction;
        if (this.polygon === "Square") {
          this.polygon = "Circle";
          geometryFunction = createRegularPolygon(4);
        } else if (this.polygon === "Box") {
          this.polygon = "Circle";
          geometryFunction = createBox();
        } else if (this.polygon === "Star") {
          this.polygon = "Circle";
          //   自定义多边形的函数
          geometryFunction = function(coordinates, geometry) {
            var center = coordinates[0];
            var last = coordinates[1];
            var dx = center[0] - last[0];
            var dy = center[1] - last[1];
            var radius = Math.sqrt(dx * dx + dy * dy);
            var rotation = Math.atan2(dy, dx);
            var newCoordinates = [];
            var numPoints = 12;
            for (var i = 0; i < numPoints; ++i) {
              var angle = rotation + (i * 2 * Math.PI) / numPoints;
              var fraction = i % 2 === 0 ? 1 : 0.5;
              var offsetX = radius * fraction * Math.cos(angle);
              var offsetY = radius * fraction * Math.sin(angle);
              newCoordinates.push([center[0] + offsetX, center[1] + offsetY]);
            }
            newCoordinates.push(newCoordinates[0].slice());
            if (!geometry) {
              geometry = new Polygon([newCoordinates]);
            } else {
              geometry.setCoordinates([newCoordinates]);
            }
            return geometry;
          };
        }

        this.draw = new Draw({
          type: this.polygon, // 图形的类型
          source: this.vectorSource, // 绘制到哪个数据源上
          geometryFunction: geometryFunction
        });

        this.map.addInteraction(this.draw);

        // 监听绘制结束的事件
        this.draw.on("drawend", ev => {
          if (!this.shallSmooth) {
            return;
          }

          if (this.polygon === "LineString") {
            // 接下来执行创建平滑曲线的操作
            let feat = ev.feature;
            let geometry = feat.getGeometry();
            let coords = geometry.getCoordinates();
            let smoothened = this.makeSmooth(coords, 20);
            geometry.setCoordinates(smoothened);
          }
        });
      }
    },

    // 制作平滑曲线的函数
    makeSmooth(path, numIterations) {
      numIterations = Math.min(Math.max(numIterations, 1), 10);
      while (numIterations > 0) {
        path = smooth(path);
        numIterations--;
      }
      return path;
    },

    // 选择框内容改变事件
    handleChange() {
      this.map.removeInteraction(this.draw);
      this.addInteractionMy();
    }
  },

  mounted() {
    this.initMap();
  }
};

/* 
    绘制点的思路:
      1. 创建出来地图后，创建矢量图层，注意把矢量图层的数据源单独抽离出来
      2. 通过Draw方法，创建绘制的点，点的数据源要使用刚才的数据源
      3. 把创建的点添加到地图的 interaction 中

    // 绘制图形不同的控制
        主要是在Draw的配置项中，type决定的

    // 如果要绘制弧线，则需要安装第三方模块， chaikin-smooth。 
       1. 安装命令: npm i chaikin-smooth --save
       2. 引入命令: import smooth from "chaikin-smooth";



*/
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
