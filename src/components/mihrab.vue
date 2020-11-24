<template>
  <div></div>
</template>

<script>
import * as THREE from "three";
import { OBJLoader, MTLLoader } from "three-obj-mtl-loader";

export default {
  name: "Mihrab",
  data() {
    return {
      THREE,
      OrbitControls: null,
      MTLLoader,
      OBJLoader,
      scene: null,
      camera: null,
      renderer: null,
      controls: null,
    };
  },
  created() {
    this.scene = new this.THREE.Scene();
    this.camera = new this.THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    this.renderer = new this.THREE.WebGLRenderer();
    this.OrbitControls = require("three-orbit-controls")(THREE);
  },
  mounted() {
    this.camera.position.z = 60;
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.$el.appendChild(this.renderer.domElement);
    this.controls = new this.OrbitControls(
      this.camera,
      this.renderer.domElement
    );
    this.controls.enableDamping = true;
    this.controls.dampingFactor = 0.25;
    this.controls.enableZoom = true;

    var keyLight = new this.THREE.DirectionalLight(
      new THREE.Color("CBECFD"),
      0.2
    );
    keyLight.position.set(-100, 0, 100);

    var fillLight = new this.THREE.DirectionalLight(
      new THREE.Color("CBECFD"),
      0.4
    );
    fillLight.position.set(100, 0, 100);

    var bottomLight = new this.THREE.DirectionalLight(
      new THREE.Color("FBFFE2"),
      0.2
    );
    bottomLight.position.set(0, 100, 100);

    var backLight = new this.THREE.DirectionalLight("CBECFD", 1.0);
    backLight.position.set(100, 0, -100).normalize();

    // white spotlight shining from the side, casting a shadow

    const spotLight = new this.THREE.SpotLight("FBFFE2", 0.4);
    spotLight.position.set(-100, 0, 100);

    spotLight.castShadow = true;

    spotLight.shadow.mapSize.width = 1024;
    spotLight.shadow.mapSize.height = 1024;

    spotLight.shadow.camera.near = 300;
    spotLight.shadow.camera.far = 2000;
    spotLight.shadow.camera.fov = 20;

    this.scene.add(spotLight);

    const bottomSpotLight = new this.THREE.SpotLight("FBFFE2", 0.4);
    bottomSpotLight.position.set(0, 100, 100);

    bottomSpotLight.castShadow = true;

    bottomSpotLight.shadow.mapSize.width = 1024;
    bottomSpotLight.shadow.mapSize.height = 1024;

    bottomSpotLight.shadow.camera.near = 300;
    bottomSpotLight.shadow.camera.far = 2000;
    bottomSpotLight.shadow.camera.fov = 20;

    this.scene.add(bottomSpotLight);
    this.scene.add(keyLight);
    this.scene.add(fillLight);
    this.scene.add(backLight);
    this.scene.add(bottomLight);

    this.addObject();

    this.animate();
  },
  methods: {
    addObject() {
      new MTLLoader()
        .setPath("/static/model/prayer-niche-mihrab/source/")
        .load("gebetsniche-100k.mtl", (materials) => {
          console.log("materials", materials);
          materials.preload();
          new OBJLoader()
            .setMaterials(materials)
            .setPath("/static/model/prayer-niche-mihrab/source/")
            .load("gebetsniche-100k.obj", (obj) => {
              obj.position.y -= 30;
              this.scene.add(obj);
            });
        });
    },
    animate() {
      requestAnimationFrame(this.animate);
      this.controls.update();
      this.renderer.render(this.scene, this.camera);
    },
  },
};
</script>

<style>
</style>