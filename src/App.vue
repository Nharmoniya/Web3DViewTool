<template>
  <div style="width: 100%;height: 100%;display: flex;flex-direction: row;justify-content: space-evenly;align-items: center;position: relative;padding-top: 40px">
    <div style="width: 100%;height: 40px;position: absolute;display: flex;flex-direction: row;top: 0">
      <div style="width: 100px;height: 40px;background: gray;border-radius: 32px;display: flex;flex-direction: row;justify-content: center;align-items: center" @click="add">x加轴</div>
      <div style="width: 100px;height: 40px;background: gray;border-radius: 32px;display: flex;flex-direction: row;justify-content: center;align-items: center" @click="minus">x减轴</div>
      <div style="width: 100px;height: 40px;background: gray;border-radius: 32px;display: flex;flex-direction: row;justify-content: center;align-items: center" @click="add1">y轴</div>
      <div style="width: 100px;height: 40px;background: gray;border-radius: 32px;display: flex;flex-direction: row;justify-content: center;align-items: center" @click="minus1">y轴</div>
      <div style="width: 100px;height: 40px;background: gray;border-radius: 32px;display: flex;flex-direction: row;justify-content: center;align-items: center" @click="add2">z轴</div>
      <div style="width: 100px;height: 40px;background: gray;border-radius: 32px;display: flex;flex-direction: row;justify-content: center;align-items: center" @click="minus2">z轴</div>
      <!-- 本案例演示导入模型 -->
    </div>
    <div id="container"/>
  </div>
</template>

<script>
import * as THREE from 'three';
// 注意OrbitControls要加{}，注意路径是jsm
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { OutlinePass } from "three/examples/jsm/postprocessing/OutlinePass.js";
// import { dat } from 'three/examples/jsm/controls/dat.gui.js';
// dat gui这个插件，是另外自己下载的，threejs的安装包里没有这个
// dat gui组件能够方便地改变某些值，并快速预览这些值的改变所产生的变化
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
let scene,camera,renderer,orbitControls,model
export default {
  name: 'Example',
  data() {
    return {
      x:0,
      y:0,
      z:0
    };
  },
  mounted() {
    this.init();
    this.loadModel();
    this.animate();
  },
  // 后续还要在beforeDestory中进行销毁
  beforeDestroy() {
    scene = null;
    renderer = null;
    camera = null;
    orbitControls = null;
    model = null;
  },
  methods: {
    add(){
      this.x += 1
      camera.position.x = this.x
      console.log('111',this.x)
    },
    minus(){
      this.x -= 1
      camera.position.x = this.x
      console.log('111',this.x)
    },
    add1(){
      this.y += 1
      camera.position.y = this.y
      console.log('111',this.y)
    },
    minus1(){
      this.y -= 1
      camera.position.y = this.y
      console.log('111',this.y)
    },
    add2(){
      this.z += 1
      camera.position.z = this.z
      console.log('111',this.z)
    },
    minus2(){
      this.z -= 1
      camera.position.z = this.z
      console.log('111',this.z)
    },
    init() {
      const container = document.getElementById('container');
      camera = new THREE.PerspectiveCamera(55, window.innerWidth / window.innerHeight, 0.1, 1000);
      // 特别注意，相机的位置要大于几何体的尺寸
      camera.position.z = 4;
      camera.position.x = 8;
      camera.position.y = 4;
      scene = new THREE.Scene();

      renderer = new THREE.WebGLRenderer({
        // 抗锯齿性
        antialias: true
      });
      // 设置背景色
      renderer.setClearColor('#ffffff', 1.0);
      renderer.setSize(800, 800);
      container.appendChild(renderer.domElement);

      // 环境光不能用来投射阴影，因为它没有方向。
      const ambientLight = new THREE.AmbientLight(0xffffff, 1); // 白光，强度为1
      scene.add(ambientLight);
      const dirLight = new THREE.DirectionalLight('rgb(253,253,253)', 5);
      dirLight.position.set(10, 10, 10); // 根据需要自行调整位置
      scene.add(dirLight);
      // 自然光
      const dirLight1 = new THREE.DirectionalLight()
      dirLight1.position.set(0, 0, 15)
      scene.add(dirLight1)
      const dirLight2 = new THREE.DirectionalLight()
      dirLight2.position.set(0, 0, -15)
      scene.add(dirLight2)
      const dirLight3 = new THREE.DirectionalLight()
      dirLight3.position.set(15, 0, 0)
      scene.add(dirLight3)
      const dirLight4 = new THREE.DirectionalLight()
      dirLight4.position.set(-15, 0, 0)
      scene.add(dirLight4)
      const dirLight5 = new THREE.DirectionalLight()
      dirLight5.position.set(0, 15, 0)
      scene.add(dirLight5)
      const dirLight6 = new THREE.DirectionalLight()
      dirLight6.position.set(0, -15, 0)
      scene.add(dirLight6)
      const dirLight7 = new THREE.DirectionalLight()
      dirLight7.position.set(5, 15, 5)
      scene.add(dirLight7)
      const dirLight8 = new THREE.DirectionalLight()
      dirLight8.position.set(-5, -15, -5)
      scene.add(dirLight8)



      // 初始化轨道控制器
      // 还需要配合animate，不断循环渲染，达到用鼠标旋转物体的作用。
      orbitControls = new OrbitControls(camera, renderer.domElement);
      orbitControls.enableRotate = false; // 禁用旋转
      // 窗口大小自适应
      window.addEventListener('resize', this.onWindowResize, false);
    },
    animate() {
      // setTimeout(() => {
      //   model.rotation.x += 0.01;
      //   model.rotation.y += 0.01;
      //   model.rotation.z += 0.01;
      // }, 500);
      requestAnimationFrame(this.animate);
      renderer.render(scene, camera);
    },
    onWindowResize() {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    },
    loadModel() {
      var loader = new GLTFLoader();
      // 路径要特别注意，默认是从public读取的，模型文件必须放在public下，并且路径前的public要省略
      loader.load('./12glb.glb', gltf => {
        // 设置模型尺寸
        model = gltf.scene;
        model.scale.set(5, 5, 5);
        model.color = '0xf3f2f8'
        model.position.x = 0;
        model.position.y = 0;
        gltf.scene.traverse((child) => {
          if (child.isMesh) {
            child.material.side = THREE.DoubleSide; // 模型双面渲染
            child.castShadow = true;  // 光照是否有阴影
            child.receiveShadow = true;  // 是否接收阴影
            child.frustumCulled = false;
          }
        });
        // model.emissive = gltf.material.color
        // model.material.emissiveMap = gltf.material.map;
        scene.add(model);
      }, undefined, error => {
        console.error(error);
      });
    }
  }
};
</script>

<style scoped>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
</style>
