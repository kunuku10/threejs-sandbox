<script setup lang="ts">
import { ref, onMounted } from "vue";
import { Mesh, MeshLambertMaterial, PerspectiveCamera, PointLight, Scene, SphereGeometry, WebGLRenderer } from "three";
import { GridHelper } from "three";

const container = ref<HTMLElement>();
const scene = new Scene();
const camera = new PerspectiveCamera();
const renderer = new WebGLRenderer();
const light = new PointLight();

const animate = () => {
  let phi = 0;
  const frame = () => {

    phi += 0.003 * Math.PI;
    camera.position.set(10 * Math.cos(phi), 10, 10 * Math.sin(phi));
    camera.lookAt(0, 0, 0);

    renderer.render(scene, camera);
    requestAnimationFrame(frame);
  }
  frame();
}

const createSphere = (): Mesh => {
  const geometry = new SphereGeometry(3);
  const material = new MeshLambertMaterial();
  return new Mesh(geometry, material);
}

const init = () => {
  if (!(container.value instanceof HTMLElement)) {
    return;
  }

  const { clientWidth, clientHeight } = container.value;

  // グリッド追加
  scene.add(new GridHelper());

  // スフィア追加
  const sphere = createSphere();
  scene.add(sphere);

  // ライト追加
  light.color.setHex(0xffffff);
  light.position.set(10, 10, 0);
  scene.add(light);

  // カメラ設定
  camera.aspect = clientWidth / clientHeight;
  camera.updateProjectionMatrix();
  camera.position.set(10, 10, 0);
  camera.lookAt(0, 0, 0);

  renderer.setSize(clientWidth, clientHeight);
  renderer.setPixelRatio(clientWidth / clientHeight);
  container.value.appendChild(renderer.domElement);

  animate();
}


onMounted(() => {
  init();
});

</script>

<template>
  <div class="ModelViewer" ref="container">
  </div>
</template>

<style scoped>
.ModelViewer {
  background-color: yellow;
  color: black;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
