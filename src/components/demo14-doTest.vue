<template>
  <div class="box1">
    <div id="mymap"></div>
    <div class="box">
      <el-button @click="showLayer( addPoint )">查看点</el-button>
      <el-button @click="showLayer( addLine )">查看路线</el-button>
      <el-button @click="showLayer( addPolygon )">查看区域</el-button>
      <el-button @click="showAll">全部显示</el-button>
    </div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View, Feature } from "ol";
import { Tile, Vector as VectorLayer } from "ol/layer";
import { OSM, Vector as VectorSource, XYZ } from "ol/source";
import { Point, LineString, Polygon } from "ol/geom";
import { Style, Fill, Stroke, Icon, Text } from "ol/style";

export default {
  data() {
    return {
      map: null,
      features: [],
      pointLayer: null,
      lineLayer: null,
      polygonLayer: null,
      coordinates: [
        {
          x: 112.87197876066057,
          y: 28.22084712811648
        },
        {
          x: 112.8720016491825,
          y: 28.225383281160706
        },
        {
          x: 112.87314605792562,
          y: 28.228450298111515
        },
        {
          x: 112.87527465926178,
          y: 28.23101377452122
        },
        {
          x: 112.87994384801641,
          y: 28.232203960351857
        },
        {
          x: 112.88353729301525,
          y: 28.23128843224413
        },
        {
          x: 112.8825531017319,
          y: 28.225932597479645
        }
      ],

      coordinates1: [
        [112.87197876066057, 28.22084712811648],
        [112.8720016491825, 28.225383281160706],
        [112.87314605792562, 28.228450298111515],
        [112.87527465926178, 28.23101377452122],
        [112.87994384801641, 28.232203960351857],
        [112.88353729301525, 28.23128843224413],
        [112.8825531017319, 28.225932597479645]
      ]
    };
  },
  methods: {
    //   初始化地图
    initMap() {
      this.map = new Map({
        target: "mymap",
        layers: [
          new Tile({
            source: new XYZ({
              url:
                "http://www.google.cn/maps/vt/pb=!1m4!1m3!1i{z}!2i{x}!3i{y}!2m3!1e0!2sm!3i345013117!3m8!2szh-CN!3scn!5e1105!12m4!1e68!2m2!1sset!2sRoadmap!4e0"
            })
          })
        ],
        view: new View({
          projection: "EPSG:4326",
          center: [112.87, 28.23],
          zoom: 13,
          maxZoom: 19,
          minZoom: 5
        })
      });
    },

    // 给地图添加矢量图层
    addPoint() {
      let pointSource = new VectorSource({
        features: this.addPointFeature()
      });
      let pointLayer = new VectorLayer({
        source: pointSource
      });
      this.map.addLayer(pointLayer);

      this.pointLayer = pointLayer;
    },

    //  给矢量图层添加要素
    addPointFeature() {
      let features = [];
      let coordinates = this.coordinates;
      for (let i = 0; i < this.coordinates.length; i++) {
        let feature = new Feature(
          new Point([coordinates[i].x, coordinates[i].y])
        );
        feature.setId(i);
        feature.setStyle(this.getStyles(feature));
        features.push(feature);
      }
      return features;
    },

    // 添加线路
    addLine() {
      let feature = new Feature({
        type: "route",
        geometry: new LineString(this.coordinates1)
      });
      feature.setStyle(
        new Style({
          stroke: new Stroke({
            width: 4,
            color: "rgba(255, 0, 0, 0.5)"
          }),
          text: new Text({
            text: "这是线路"
          })
        })
      );

      let lineSource = new VectorSource({
        features: [feature]
      });
      let lineLayer = new VectorLayer({
        source: lineSource
      });
      this.map.addLayer(lineLayer);

      this.lineLayer = lineLayer;
    },

    // 添加多边形
    addPolygon() {
      let feature = new Feature({
        type: "polygon",
        geometry: new Polygon([this.coordinates1])
      });
      feature.setStyle(
        new Style({
          stroke: new Stroke({
            width: 5,
            color: "rgba(255, 255, 0, 0.8s)"
          }),
          fill: new Fill({
            color: [248, 172, 166, 0.6]
          }),
          text: new Text({
            text: "这是区域"
          })
        })
      );

      let polygonSource = new VectorSource({
        features: [feature]
      });

      let polygonLayer = new VectorLayer({
        source: polygonSource
      });

      this.map.addLayer(polygonLayer);
      this.polygonLayer = polygonLayer;
    },

    // 设置样式
    getStyles(feature) {
      let Styles = [];
      // 绘制圆角矩形
      let canvas = document.createElement("canvas");
      let context = canvas.getContext("2d");
      let length = (feature.id_ + "标记点").length + 2;
      canvas.width = length * 15;
      canvas.height = 35;
      let x = 0;
      let y = 0;
      let w = canvas.width;
      let h = canvas.height;
      let r = 15;
      // 缩放
      context.scale(0.8, 0.8);
      context.fillStyle = "rgba(255,255,255,1)";
      // 绘制圆角矩形
      context.beginPath();
      context.moveTo(x + r, y);
      context.arcTo(x + w, y, x + w, y + h, r);
      context.arcTo(x + w, y + h, x, y + h, r);
      context.arcTo(x, y + h, x, y, r);
      context.arcTo(x, y, x + w, y, r);
      // 设置阴影
      context.shadowColor = "rgba(0, 0, 0, 0.2)"; // 颜色
      context.shadowBlur = 5; // 模糊尺寸
      context.shadowOffsetX = 2; // 阴影Y轴偏移
      context.shadowOffsetY = 2; // 阴影X轴偏移
      // ----
      context.closePath();
      // 填充
      context.fill();
      // 设置style
      Styles.push(
        new Style({
          image: new Icon({
            img: canvas,
            imgSize: [w, h],
            anchor: [0, 1]
          }),
          text: new Text({
            textAlign: "center",
            text: "标记点" + feature.id_,
            offsetX: 35,
            offsetY: -18
          }),
          zIndex: feature.id_
        })
      );
      Styles.push(
        new Style({
          image: new Icon({
            src: "http://weilin.me/ol3-primer/img/anchor.png",
            anchor: [0.5, 1]
          }),
          zIndex: feature.id_
        })
      );
      return Styles;
    },

    // 清除已存在图层，清空对应对象
    clearLayer(){
      this.map.removeLayer(this.pointLayer);
      this.map.removeLayer(this.lineLayer);
      this.map.removeLayer(this.polygonLayer);
      this.pointLayer = null;
      this.lineLayer = null;
      this.polygonLayer = null;
    },

    // 显示指定的图层
    showLayer(param) {
        this.clearLayer();
      param();
    },

    // 显示全部图层
    showAll(){
       this.clearLayer();
       this.addPoint();
       this.addLine();
       this.addPolygon();
    }
  },
  mounted() {
    this.initMap();
  }
};
</script>

<style>
</style>
