<template>
  <div id="app">

  </div>
</template>

<script>
import * as THREE from "three"
import {Vector3} from "three";
export default {
  name: 'App',
  data: function() {
    return {
      scene: new THREE.Scene(),
      render: new THREE.WebGLRenderer(),
      updateList: [],
      delta: new THREE.Clock()
    }
  },
  mounted() {
    this.render.setSize(this.$el.clientWidth, this.$el.clientHeight)
    this.$el.appendChild(this.render.domElement)
    this.camera = new THREE.PerspectiveCamera(75, this.$el.clientWidth / this.$el.clientHeight, 0.1, 1000)
    this.setupCamera()
    this.utils()
    this.createEllipse(250)
    this.createEllipse(200)
    this.createEllipse(150)
    this.createSphere(200)
    this.updateLoop()

  },
  methods: {
    createEllipse: function (size) {
      let ellipseGeometry = new THREE.EllipseCurve(
          0, 0,
          size, size,
          0, 2 * Math.PI,
      )
      let geometry = new THREE.BufferGeometry().setFromPoints(ellipseGeometry.getPoints(50))
      let material = new THREE.LineBasicMaterial({
        color: 0x00aaaa
      })
      let ellipse = new THREE.Line(geometry, material)

      let animationTime = 4
      let keyframeTrackX = new THREE.NumberKeyframeTrack(".scale[x]", [0, animationTime], [0, 1])
      let keyframeTrackY = new THREE.NumberKeyframeTrack(".scale[y]", [0, animationTime], [0, 1])
      let animationClip = new THREE.AnimationClip(null, animationTime, [keyframeTrackX, keyframeTrackY])
      let ellipseMixer = new THREE.AnimationMixer(ellipse)
      let animationAction = ellipseMixer.clipAction(animationClip)
      animationAction.play()
      this.registerAnimationUpdate(ellipseMixer)
      this.scene.add(ellipse)

      var sphere = new THREE.SphereGeometry();
      var object = new THREE.Mesh( sphere, new THREE.MeshBasicMaterial( 0xff0000 ) );
      var box = new THREE.BoxHelper( object, 0xffff00 );
      box.setFromObject(ellipse)
      this.scene.add( box );
    },
    createSphere: function (size) {
      let geometry = new THREE.SphereBufferGeometry(size, 10, 10)
      let material = new THREE.MeshBasicMaterial({
        color: 0x00aaaa
      })
      material.wireframe = true
      material.wireframeLinewidth = 1
      material.wireframeLinejoin = "bevel"
      let sphere = new THREE.Mesh(geometry, material)

      let animationTime = 20
      let keyframeTrackY = new THREE.NumberKeyframeTrack(".rotation[y]", [0, animationTime], [0, Math.PI * 2])
      let animationClip = new THREE.AnimationClip(null, animationTime, [keyframeTrackY])
      let sphereMixer = new THREE.AnimationMixer(sphere)
      let animationAction = sphereMixer.clipAction(animationClip)

      sphere.rotation.x = Math.PI * 0.5

      animationAction.play()
      this.registerAnimationUpdate(sphereMixer)

      this.scene.add(sphere)
    },
    setupCamera: function () {
      this.camera.position.y = -400
      this.camera.position.z = 200
      this.camera.lookAt(new Vector3(0, 0, 0))
    },
    updateLoop: function () {
      let delta = this.delta.getDelta()
      this.updateList.forEach(item => {
        item.update(delta)
      })
      this.render.render(this.scene, this.camera)
      requestAnimationFrame(this.updateLoop)
    },
    registerAnimationUpdate: function (mixer) {
      this.updateList.push(mixer)
    },
    utils: function () {
      let axes = new THREE.AxesHelper(300)
      this.scene.add(axes)
    }
  }
}
</script>

<style>
#app {
  width: 800px;
  height: 800px;
}
</style>
