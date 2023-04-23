<template>
  <div ref="model" class="model"></div>
</template>

<script>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

export default {
  props: {
    W: {
      type: Number,
      default: 0,
    },
    H: {
      type: Number,
      default: 0,
    },
    pathFile: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      scene: null,
      camera: null,
      renderer: null,
      model: null,
    };
  },

  methods: {
    render() {
      requestAnimationFrame(this.render);
      const currentTimeLine = window.pageYOffset / 400;

      if (currentTimeLine >= 1.2) {
        if (this.model !== null && currentTimeLine <= 3) {
          this.model.rotation.y = -(currentTimeLine - 1.2);
        }
      }

      this.renderer.render(this.scene, this.camera);
    },

    loadGLTF(path, onLoaded) {
      const loader = new GLTFLoader();
      loader.load(path, (gltf) => {
        onLoaded(gltf);
      });
    },

    inti() {
      this.renderer.setClearColor(0x1f1f1f);
      this.camera.position.set(0, 0, 500);

      let light = new THREE.PointLight(0xffffff, 1, 1000);
      light.position.y = 200;
      light.position.z = 250;
      this.scene.add(light);

      const hemi = new THREE.HemisphereLight(0xffffff, 0x444444);
      hemi.position.set(0, 300, 300);
      this.scene.add(hemi);

      // Model Loader
      this.loadGLTF(this.pathFile, (data) => {
        this.model = data.scene;
        this.model.scale.set(120, 120, 120);
        this.model.rotation.y = -0.0025;
        this.scene.add(this.model);
      });
    },
  },

  mounted() {
    this.scene = new THREE.Scene();
    this.camera = new THREE.PerspectiveCamera(75, this.W / this.H, 1.3);
    this.renderer = new THREE.WebGL1Renderer({ antialias: true });
    this.renderer.setPixelRatio(window.devicePixelRatio);
    this.renderer.setSize(this.W, this.H);

    this.$refs["model"].appendChild(this.renderer.domElement);

    this.inti();
    this.render();
  },
};
</script>

<style>
.model {
  width: 100%;
  height: 100vh;
}
</style>
