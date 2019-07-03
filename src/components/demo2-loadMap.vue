<template>
  <div class="box1">
    <el-row style="margin:20px;">
      <el-col :span="8">
        <el-select v-model="mapType" placeholder="请选择" @change="handleChange" style="width:100%">
          <el-option value="openStreetMapLayer">OpenStreetMap地图</el-option>
          <el-option value="bingMapLayer">Bing地图</el-option>
          <el-option value="stamenLayer">Stamen地图</el-option>
          <el-option value="loadTileMap">在线瓦片地图之OSM</el-option>
          <el-option value="gaodeMapLayer">在线瓦片地图之高德地图</el-option>
          <el-option value="baiduMapLayer">在线瓦片地图之百度地图</el-option>
          <el-option value="bingMapLayer">在线瓦片地图之微软Bing中文地图</el-option>
          <el-option value="googleMapLayer">在线瓦片地图之google地图</el-option>
          <el-option value="offLineMapLayer">离线瓦片地图加载方式</el-option>
        </el-select>
      </el-col>
    </el-row>
    <div id="mymap"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import Tile from "ol/layer/Tile";
import { OSM, BingMaps, Stamen, MapQuest, XYZ, TileImage } from "ol/source";
import TileGrid from "ol/tilegrid/TileGrid";

export default {
  data() {
    return {
      map: null,
      openStreetMapLayer: null,
      bingMapLayer: null,
      stamenLayer: null,
      loadTileMap: null,
      gaodeMapLayer: null,
      baiduMapLayer: null,
      bingMapLayer: null,
      googleMapLayer: null,
      offLineMapLayer: null,
      mapType: "openStreetMapLayer"
    };
  },
  methods: {
    //  初始化地图
    initMap() {
      this.createLayer();
      this.map = new Map({
        target: "mymap",
        layers: [this.openStreetMapLayer],
        view: new View({
          center: [104.06, 30.67],
          projection: "EPSG:4326",
          zoom: 10
        })
      });
    },

    // 创建图层的函数
    createLayer() {
      // 1. Open Street Map 地图层
      this.openStreetMapLayer = new Tile({
        source: new OSM()
      });

      // 2. Bing地图层
      this.bingMapLayer = new Tile({
        source: new BingMaps({
          key:
            "AjaCh0-yFjaLX9djzmFR6U1jaSk9tpCBiYu2hnBpQUIPAtgiNtxr_nRCJ9qFHMNv",
          // "AkjzA7OhS4MIBjutL21bkAop7dc41HSE0CNTR5c6HJy8JKc7U9U9RveWJrylD3XJ",
          imagerySet: "Road"
        })
      });

      // 3. Stamen地图层
      this.stamenLayer = new Tile({
        source: new Stamen({
          layer: "watercolor"
        })
      });

      // 4. 加载瓦片地图OSM
      this.loadTileMap = new Tile({
        source: new XYZ({
          url: "http://{a-c}.tile.openstreetmap.org/{z}/{x}/{y}.png"
        })
      });

      // 5. 高德地图层
      this.gaodeMapLayer = new Tile({
        source: new XYZ({
          url:
            "http://webst0{1-4}.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=7&x={x}&y={y}&z={z}"
        })
      });

      // 自定义分辨率和瓦片坐标系
      var resolutions = [];
      var maxZoom = 18;

      // 计算百度使用的分辨率
      for (var i = 0; i <= maxZoom; i++) {
        resolutions[i] = Math.pow(2, maxZoom - i);
      }
      var tilegrid = new TileGrid({
        origin: [0, 0], // 设置原点坐标
        resolutions: resolutions // 设置分辨率
      });

      // 6. 百度地图层
      this.baiduMapLayer = new Tile({
        source: new TileImage({
          // 设置投影
          projection: "EPSG:4326",
          // 转换坐标系
          tileGrid: tilegrid,
          tileUrlFunction: function(tileCoord) {
            console.log(tileCoord);
            // 参数tileCoord为瓦片坐标
            var z = tileCoord[0];
            var x = tileCoord[1];
            var y = tileCoord[2];

            // 计算当前层级下瓦片总数的一半，用于定位整个地图的中心点
            var halfTileNum = Math.pow(2, z - 1);
            // 原点移到中心点后，计算xy方向上新的坐标位置
            var baiduX = x - halfTileNum;
            var baiduY = y + halfTileNum;

            // 百度瓦片服务url将负数使用M前缀来标识
            if (baiduX < 0) {
              baiduX = "M" + -baiduX;
            }
            if (baiduY < 0) {
              baiduY = "M" + -baiduY;
            }
            // 返回经过转换后，对应于百度在线瓦片的url
            return (
              "http://online3.map.bdimg.com/onlinelabel/?qt=tile&x=" +
              baiduX +
              "&y=" +
              baiduY +
              "&z=" +
              z +
              "&styles=pl&udt=20151021&scaler=1&p=1"
            );
          }
        })
      });

      // 7. 微软Bing中文地图
      this.bingMapLayer = new Tile({
        source: new XYZ({
          tileUrlFunction: function(tileCoord) {
            var z = tileCoord[0];
            var x = tileCoord[1];
            var y = -tileCoord[2] - 1;
            var result = "",
              zIndex = 0;

            for (; zIndex < z; zIndex++) {
              result = ((x & 1) + 2 * (y & 1)).toString() + result;
              x >>= 1;
              y >>= 1;
            }
            return (
              "http://dynamic.t0.tiles.ditu.live.com/comp/ch/" +
              result +
              "?it=G,VE,BX,L,LA&mkt=zh-cn,syr&n=z&og=111&ur=CN"
            );
          }
        })
      });

      // 8. google地图加载
      this.googleMapLayer = new Tile({
        source: new XYZ({
          url:
            "http://www.google.cn/maps/vt/pb=!1m4!1m3!1i{z}!2i{x}!3i{y}!2m3!1e0!2sm!3i345013117!3m8!2szh-CN!3scn!5e1105!12m4!1e68!2m2!1sset!2sRoadmap!4e0"
        })
      });

      // 9. 离线瓦片地图加载
      // https://b.tile.openstreetmap.org/10/809/420.png
      this.offLineMapLayer = new Tile({
        source: new XYZ({
          url: '/{z}/{x}/{y}.png'
        })
      });
    },

    // 处理选项改变的函数
    handleChange() {
      switch (this.mapType) {
        case "openStreetMapLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.openStreetMapLayer);
          break;
        case "bingMapLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.bingMapLayer);
          break;
        case "stamenLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.stamenLayer);
          break;
        case "loadTileMap":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.loadTileMap);
          break;
        case "gaodeMapLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.gaodeMapLayer);
          break;
        case "baiduMapLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.baiduMapLayer);
          break;
        case "bingMapLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.bingMapLayer);
          break;
        case "googleMapLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.googleMapLayer);
          break;
        case "offLineMapLayer":
          this.map.removeLayer(this.map.getLayers().item(0));
          this.map.addLayer(this.offLineMapLayer);
          break;
      }
    }
  },

  mounted() {
    this.initMap();

    /* 

    瓦片的url解析对于想直接使用在线瓦片服务的开发者而言，是一项经常要做的事。根据难度，大致可以分为三种情况：

      第一种 : 是最简单的，请求瓦片的url明确有xyz参数，比如高德地图和百度地图。
      第二种 : 稍微难一点，xyz作为路径直接存在于url里面，没有明确的参数表明哪些是xyz，
              比如Open Street Map和Yahoo地图，这种情况下，地图服务器接收到请求后，
              就直接在服务器按照这个路径获取图片，按照这个逻辑，一般第一个参数表示是z，
              第二个参数为x，第三个参数为y。
              要想确认是否真是这样，可以写一个小程序来验证一下，如果还有问题，
              建议按照上面分析地图坐标系中的方法，从z比较小的情况入手来分析x，y，z的位置。
      第三种 : 则最难，地图服务提供商为了防止大家直接非法使用瓦片地图，
              对瓦片的url进行了加密，比如现在的微软Bing中文地图和Google地图，
              这种情况下只有知道如何解密才能使用。

    加载在线瓦片地图与离线瓦片地图的区别:

      a. 离线瓦片地图和在线瓦片地图是一样的原理，都是瓦片，只是离线瓦片地图的存取方式，是由开发者自己来定义的，而在线瓦片地图则不一定。 
      b. url必须根据瓦片地图存放路径来编写，比如这个例子里面，
        10表示的是层级，809表示的是x，420表示的是y，我们的url参数就写成： {z}/{x}/{y}.png。 
        如果瓦片地图都放在一个目录下，采用z-x-y.png的方式命名，那么url参数就得写成： {z}-{x}-{y}.png。

    */
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
