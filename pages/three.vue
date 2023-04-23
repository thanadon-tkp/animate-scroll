<template>
  <div>
    <div ref="stat" class="stat"></div>
    <div class="debug">{{ debug }}</div>
    <div class="title">
      <h1>Scroll To Rotation</h1>
    </div>
    <div ref="model" class="model"></div>
    <div class="footer">
      <h1>My Cat1 ...</h1>
    </div>
    <div class="footer">
      <h1>My Cat2 ...</h1>
    </div>
    <div class="footer">
      <h1>My Cat3 ...</h1>
    </div>
    <div class="footer">
      <h1>My Cat4 ...</h1>
    </div>
  </div>
</template>

<script>
import * as THREE from "three";
import * as Stats from "stats-js";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

export default {
  data() {
    return {
      W: window.innerWidth,
      H: window.innerHeight,
      scene: null,
      camera: null,
      renderer: null,

      stat: new Stats(),
      debug: "debug:",

      light: null,

      sphere: null,

      myModel: null,

      controls: null,

    };
  },

  methods: {
    render() {
      requestAnimationFrame(this.render);
      this.stat.begin();
      const currentTimeLine = window.pageYOffset / 400;

      // this.controls.update();

      // this.sphere.rotation.y += 0.01;

      if(this.myModel !== null && currentTimeLine <= 1) {
        this.myModel.rotation.y = -(currentTimeLine + 0.4);
      }

      this.debug = `debug: currentTimeLine(${currentTimeLine})`;

      this.stat.end();
      this.renderer.render(this.scene, this.camera);
    },

    loadGLTF(model, onLoaded) {
      const loader = new GLTFLoader();
      loader.load(model.path, (gltf) => {
        onLoaded(gltf);
      });
    },

    inti() {
      this.renderer.setClearColor(0x1f1f1f);
      this.camera.position.set(0, 0, 500);

      this.light = new THREE.PointLight(0xffffff, 1, 1000);
      this.light.position.y = 200;
      this.light.position.z = 250;
      this.scene.add(this.light);

      const hemi = new THREE.HemisphereLight(0xffffff, 0x444444);
      hemi.position.set(0, 300, 300);
      this.scene.add(hemi);

      // Model Loader
      const model = {
        path: "/cat/scene.gltf",
      };
      this.loadGLTF(model, (data) => {
        this.myModel = data.scene;
        this.myModel.scale.set(120, 120, 120);
        this.scene.add(this.myModel);
      });

      // let geo = new THREE.SphereGeometry(100, 10, 10);
      // let mat = new THREE.MeshBasicMaterial({
      //   color: 0x00ff00,
      //   wireframe: true,
      // });

      // this.sphere = new THREE.Mesh(geo, mat);
      // this.scene.add(this.sphere);

      // controls
      // this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      // this.controls.enableDamping = true;
      // this.controls.dampingFactor = 0.125;
    },

  },

  mounted() {
    this.scene = new THREE.Scene();
    this.camera = new THREE.PerspectiveCamera(75, this.W / this.H, 1.3);
    this.renderer = new THREE.WebGL1Renderer({ antialias:true});
    this.renderer.setPixelRatio(window.devicePixelRatio);
    this.renderer.setSize(this.W, this.H);

    this.$refs["model"].appendChild(this.renderer.domElement);

    this.stat.setMode(0);
    this.$refs["stat"].appendChild(this.stat.domElement);

    this.inti();
    this.render();

  },
  
};
</script>

<style>
body {
  margin: 0;
  background-color: #000;
}

.stat {
  position: absolute;
  top: 0;
  left: 0;
}

.title {
  margin: 42px 0;
  display: flex;
  justify-content: center;
}
h1 {
  color: #fff;
}

.model {
  width: 100%;
  height: 100vh;
}

.footer {
  display: flex;
  justify-content: center;
  margin: 42px 0;
}

.debug {
  position: fixed;
  top: 0;
  left: 100px;
  width: 500px;
  height: 30px;
  font-size: 20px;
  color: white;
}
</style>
