<template>
  <div id="app">
    <el-container class="app-cont">
      <el-aside style="backgroundColor:rgb(84, 92, 100);">
        <!-- 左侧的侧边栏导航区域 -->
        <aside-nav @change:component="doChange"></aside-nav>
      </el-aside>
      <el-container>

        <!-- 动态组件部分 -->
        <component :is="currComponent"></component>

      </el-container>
    </el-container>
  </div>
</template>

<script>

import asideNav from './components/AsideNav/AsideNav'
import demo1 from "./components/demo1-basic"
import demo2 from "./components/demo2-loadMap"
import demo3 from "./components/demo3-Draw"
import demo4 from "./components/demo4-Controls"
import demo5 from "./components/demo5-Overlay"
import demo6 from "./components/demo6-DefineAlertModel"
import demo7 from "./components/demo7-PointFeature"
import demo8 from "./components/demo8-TileArcGISRest"
import demo9 from "./components/demo9-Collapsible"
import demo10 from "./components/demo10-CustomDefineInteraction"
import demo11 from "./components/demo11-Measure"
import demo12 from "./components/demo12-HeatMap"
import demo13 from "./components/demo13-defineEvent"
import demo14 from "./components/demo14-doTest"
import demo15 from "./components/demo15-clickFeatureShowInfo"
import demo16 from "./components/demo16-drawDemo"

export default {
  name: 'app',
  data(){
    return {
      currComponent: "demo1"
    }
  },
  components: {
    asideNav,
    demo1, demo2, demo3, demo4, demo5, demo6, demo7, demo8, demo9, demo10, demo11, demo12, demo13, demo14, demo15, demo16
  },
  methods:{
    doChange( name ){
      this.currComponent = name;
    }
  }
}
</script>

<style>
#app{
  height: 100%;
}
.app-cont{
  height: 100%;
}
</style>
