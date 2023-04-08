<script setup lang="ts">
import {
  Scene,
  PerspectiveCamera,
  WebGLRenderer,
  Color,
  AxesHelper,
  PlaneGeometry,
  MeshBasicMaterial,
  Mesh,
  BoxGeometry,
  SphereGeometry,
  MeshLambertMaterial,
  SpotLight,
} from "three";
import States from "stats.js";
import { onMounted, ref } from "vue";

const threeRef = ref();
const statesRef = ref();
const staRef = ref<States>();
function initState() {
  staRef.value = new States();
  staRef.value.showPanel(1);
  if (statesRef) {
    statesRef.value.appendChild(staRef.value.dom);
  }
}

function initScene() {
  initState();
  //背景  或者说是画布
  const scene = new Scene();

  // 坐标系
  const axes = new AxesHelper(20);
  scene.add(axes);

  //平面几何
  const planeGeometry = new PlaneGeometry(60, 20);
  //基础材质
  const planeMaterial = new MeshLambertMaterial({ color: 0xcccccc });
  // 平面图
  const plane = new Mesh(planeGeometry, planeMaterial);
  // 接收阴影
  plane.receiveShadow = true;
  // 旋转角度
  plane.rotation.x = -0.5 * Math.PI;
  // plane.rotation.y = 0;
  // plane.rotation.z = 0;
  // 位置偏移  从平面中心偏移
  plane.position.x = 15;
  // plane.position.y = 0;
  // plane.position.z = 0;
  scene.add(plane);
  //盒子形状几何体
  const cubeGeometry = new BoxGeometry(4, 4, 4);
  // 材质
  const cubeMaterial = new MeshLambertMaterial({ color: 0xff0000 });

  const cube = new Mesh(cubeGeometry, cubeMaterial);
  cube.position.x = 2;
  cube.position.y = 2;
  cube.position.z = 2;
  // 阴影
  cube.castShadow = true;
  scene.add(cube);
  // 球类缓冲几何体
  const geometry = new SphereGeometry(4, 20, 20);
  const material = new MeshLambertMaterial({ color: 0xffff00 });
  const sphere = new Mesh(geometry, material);
  sphere.position.x = -10;
  sphere.position.y = 4;
  sphere.position.z = 2;
  sphere.castShadow = true;
  scene.add(sphere);

  //聚光灯 --光源
  const spotLight = new SpotLight(0xffffff);
  spotLight.position.set(-40, 60, 10);
  spotLight.castShadow = true;
  scene.add(spotLight);
  // 渲染画布
  const renderer = new WebGLRenderer();
  renderer.setClearColor(new Color(0xeeeeee));
  renderer.setSize(window.innerHeight, window.innerWidth);
  // 渲染开启阴影
  renderer.shadowMap.enabled = true;
  // 摄像机
  const camera = new PerspectiveCamera(
    45,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
  );
  // 摄像机角度
  camera.position.x = -30;
  camera.position.y = 40;
  camera.position.z = 30;

  // 启用
  camera.lookAt(scene.position);
  // 添加并挂载
  threeRef.value.appendChild(renderer.domElement);
  let step = 0;
  renderScene();
  function renderScene() {
    // 开始统计
    staRef.value?.begin();
    cube.rotation.x += 0.02;
    cube.rotation.y += 0.02;
    cube.rotation.z += 0.02;

    step += 0.04;

    sphere.position.x = 20 + 10 * Math.cos(step);
    sphere.position.y = 2 + 10 * Math.abs(Math.sin(step));
    // 结束统计
    staRef.value?.end();
    requestAnimationFrame(renderScene);
    renderer.render(scene, camera);
  }
}
onMounted(() => {
  initScene();
});
</script>

<template>
  <div ref="statesRef"></div>
  <div ref="threeRef"></div>
</template>

<style scoped></style>
