<template>
  <el-menu
    default-active="1-1"
    class="el-menu-vertical-demo"
    background-color="#545c64"
    text-color="#fff"
    active-text-color="#ffd04b"
  >
    <el-submenu index="1">
      <template slot="title">
        <i class="el-icon-location"></i>
        <span>基本使用demo</span>
      </template>
      <el-menu-item-group>
        <el-menu-item
          :index=" '1-' + ( index + 1 )"
          v-for="(nav, index) in navList"
          :key="index"
          @click="changeCont( nav.component )"
        >{{ nav.name }}</el-menu-item>
      </el-menu-item-group>
    </el-submenu>
  </el-menu>
</template>

<script>
export default {
  data() {
    return {
      navList: [
        {
          name: "1. 构建基础的地图",
          component: "demo1"
        },
        {
          name: "2. 加载不同类型的地图",
          component: "demo2"
        },{
          name: "3. 在地图上绘制点线面功能",
          component: "demo3"
        },{
          name: "4. 地图上的鼠标位置控制",
          component: "demo4"
        },{
          name: "5. 地图上添加覆盖物",
          component: "demo5"
        },{
          name: "6. 显示标记，点击弹窗",
          component: "demo6"
        },{
          name: "7. 点要素群集功能",
          component: "demo7"
        },{
          name: "8. ArcGIS 作为切片实现的平铺功能",
          component: "demo8"
        },{
          name: "9. 折叠右下角的内容实例",
          component: "demo9"
        },{
          name: "10. 自定义交互操作",
          component: "demo10"
        },{
          name: "11. 测量的实现方式",
          component: "demo11"
        },{
          name: "12. 热力图的实现",
          component: "demo12"
        },{
          name: "13. 自定义事件",
          component: "demo13"
        },{
          name: "14. 复习基础类",
          component: "demo14"
        },{
          name: "15. 点击要素查看信息",
          component: "demo15"
        },{
          name: "16. 绘制点线面demo",
          component: "demo16"
        }
      ]
    };
  },
  methods: {
    changeCont( name ){
      this.$emit("change:component", name);
    }
  }
};
</script>

<style>
</style>
