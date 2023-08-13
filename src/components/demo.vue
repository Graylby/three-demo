<script setup>
import * as THREE from "three";
import front from '../assets/front.png';
import aside from '../assets/aside.png';
import bottom from '../assets/bottom.png';
// 引入dat.gui.js的一个类GUI
import { GUI } from 'three/addons/libs/lil-gui.module.min.js';
// 引入轨道控制器扩展库OrbitControls.js
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { onMounted, ref } from "vue";

const container = new ref();
const init = () => {
  // 设置场景
  const scene = new THREE.Scene();
  const geometry = new THREE.BoxGeometry(160, 350, 160);
  // 设置gui
  const gui = new GUI();
  gui.domElement.style.right = '0px';
  gui.domElement.style.width = '300px';
  // 设置纹理贴图
  // 创建纹理对象并加载纹理图像
  const textureFront = new THREE.TextureLoader().load(front);
  const textureBack = new THREE.TextureLoader().load(front);
  const textureTop = new THREE.TextureLoader().load(bottom);
  const textureBottom = new THREE.TextureLoader().load(bottom);
  const textureLeft = new THREE.TextureLoader().load(aside);
  const textureRight = new THREE.TextureLoader().load(aside);
  // 创建材质并将纹理应用到每个面
  const materialFront = new THREE.MeshLambertMaterial({ map: textureFront });
  const materialBack = new THREE.MeshLambertMaterial({ map: textureBack });
  const materialTop = new THREE.MeshLambertMaterial({ map: textureTop });
  const materialBottom = new THREE.MeshLambertMaterial({ map: textureBottom });
  const materialLeft = new THREE.MeshLambertMaterial({ map: textureLeft });
  const materialRight = new THREE.MeshLambertMaterial({ map: textureRight });
  const materials = [
    materialRight,    // 右侧面
    materialLeft,     // 左侧面
    materialTop,      // 顶部面
    materialBottom,   // 底部面
    materialFront,    // 前面
    materialBack      // 后面
  ]
  const mesh = new THREE.Mesh(geometry, materials);
  mesh.position.set(0, 10, 0);
  scene.add(mesh);
  // 设置相机
  const width = 1000;
  const height = 1000;
  const camera = new THREE.PerspectiveCamera(30, width / height, 1, 3000);
  camera.position.set(600, 300, 600);
  camera.lookAt(mesh.position);
  // 设置xyz坐标轴
  const axesHelper = new THREE.AxesHelper(1500);
  scene.add(axesHelper);
  // 点光源
  const light = new THREE.PointLight( 0xffffff, 0.8 );
  light.position.set( 0, 300, 200 );
  gui.add(light.position, 'x', -500, 500)
  gui.add(light.position, 'y', -500, 500)
  gui.add(light.position, 'z', -500, 500)
  const pointLightHelper = new THREE.PointLightHelper( light, 10 );
  scene.add( pointLightHelper );
  scene.add( light );
  //环境光:没有特定方向，整体改变场景的光照明暗
  const ambient = new THREE.AmbientLight(0xffffff, 0.4);
  scene.add(ambient);
  // 渲染
  const renderer = new THREE.WebGLRenderer({
    antialias: true,
  });
  renderer.setSize(width, height);
  renderer.setClearColor(0x444444);
  renderer.setPixelRatio(window.devicePixelRatio);
  // 设置相机控件轨道控制器OrbitControls
  const controls = new OrbitControls(camera, renderer.domElement);
  controls.addEventListener("change", function () {
    renderer.render(scene, camera);
  });
  container.value.appendChild(renderer.domElement);
  setTimeout(()=>{renderer.render(scene, camera)},200)

};

onMounted(() => {
  init();
});
</script>

<template>
  <div class="wrapper">
    <div ref="container"></div>
  </div>
</template>

<style scoped>
.wrapper {
  width: 1000px;
  height: 1000px;
}
</style>
