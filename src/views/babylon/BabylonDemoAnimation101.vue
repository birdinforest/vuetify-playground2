<template>
  <v-container>
    <v-row justify="center"><h2>babylon animation 101</h2></v-row>
    <v-row justify="center">
      <v-col cols="6">Find the original code from: <a href="https://playground.babylonjs.com#QYFDDP#1">here</a></v-col>
      <v-col col="6"><v-btn @click="changeColor" color="primary">Change Torus Color</v-btn></v-col>
    </v-row>
    <v-row>
      <canvas id="renderCanvas"></canvas>
    </v-row>
  </v-container>
</template>
<script lang="ts">
import Vue from "vue";
import * as BABYLON from "babylonjs";

export default Vue.extend({
  mounted() {
    this.createScene();
  },
  data: () => ({
    color: new BABYLON.Color3(0, 0.3, 0.5),
  }),
  methods: {
    changeColor() {
      this.color.r += 0.3;
      if (this.color.r > 1) this.color.r -= 1;
      console.log("color.r = ", this.color.r);
    },
    createScene() {
      const canvas: HTMLCanvasElement = document.getElementById("renderCanvas") as HTMLCanvasElement;
      const engine: BABYLON.Engine = new BABYLON.Engine(canvas, true);

      // This creates a basic Babylon Scene object (non-mesh)
      const scene = this.createSceneInternal(canvas, engine);

      engine.runRenderLoop(() => {
        scene.render();
      });
    },
    createSceneInternal(canvas: HTMLCanvasElement, engine: BABYLON.Engine): BABYLON.Scene {
      const scene = new BABYLON.Scene(engine);

      const light = new BABYLON.PointLight("Omni", new BABYLON.Vector3(0, 100, 100), scene);
      const camera = new BABYLON.ArcRotateCamera("Camera", 0, 0.8, 100, BABYLON.Vector3.Zero(), scene);
      camera.attachControl(canvas, true);

      //Boxes
      const box1 = BABYLON.Mesh.CreateBox("Box1", 10.0, scene);
      box1.position.x = -20;
      const materialBox = new BABYLON.StandardMaterial("texture1", scene);
      materialBox.diffuseColor = new BABYLON.Color3(0, 1, 0); //Green
      box1.material = materialBox;

      const box2 = BABYLON.Mesh.CreateBox("Box2", 10.0, scene);
      box2.position.x = 20;
      const materialBox2 = new BABYLON.StandardMaterial("texture2", scene);
      box2.material = materialBox2;

      const obj = BABYLON.Mesh.CreateTorus("obj1", 15, 5, 6, scene);
      obj.position.y = 30;
      const materialObj1 = new BABYLON.StandardMaterial("texture3", scene);
      materialObj1.diffuseColor = this.color; //new BABYLON.Color3(0.5, 0, 0);
      obj.material = materialObj1;

      // Creation of a basic animation with box 1
      //----------------------------------------

      //Create a scaling animation at 30 FPS
      const animationBox = new BABYLON.Animation(
        "tutoAnimation",
        "scaling.x",
        30,
        BABYLON.Animation.ANIMATIONTYPE_FLOAT,
        BABYLON.Animation.ANIMATIONLOOPMODE_CYCLE
      );
      //Here we have chosen a loop mode, but you can change to :
      //  Use previous values and increment it (BABYLON.Animation.ANIMATIONLOOPMODE_RELATIVE)
      //  Restart from initial value (BABYLON.Animation.ANIMATIONLOOPMODE_CYCLE)
      //  Keep the final value (BABYLON.Animation.ANIMATIONLOOPMODE_CONSTANT)

      // Animation keys
      const keys = [];
      //At the animation key 0, the value of scaling is "1"
      keys.push({
        frame: 0,
        value: 1,
      });

      //At the animation key 20, the value of scaling is "0.2"
      keys.push({
        frame: 20,
        value: 0.2,
      });

      //At the animation key 100, the value of scaling is "1"
      keys.push({
        frame: 100,
        value: 1,
      });

      //Adding keys to the animation object
      animationBox.setKeys(keys);

      //Then add the animation object to box1
      box1.animations.push(animationBox);

      //Finally, launch animations on box1, from key 0 to key 100 with loop activated
      scene.beginAnimation(box1, 0, 100, true);

      // Creation of a manual animation with box 2
      //------------------------------------------
      setInterval(function() {
        //The color is defined at run time with random()
        materialBox2.diffuseColor = new BABYLON.Color3(Math.random(), Math.random(), Math.random());
      }, 1000);

      return scene;
    },
  },
});
</script>
<style scoped>
#renderCanvas {
  width: 100%;
  height: 100%;
  touch-action: none;
  /* border: 1px solid red; */
}
</style>
