<template>
  <div class="webgl-component" ref="container"></div>
</template>

<script lang="ts">
/* eslint-disable @typescript-eslint/no-unused-vars */

import { onMounted, ref, onUnmounted } from 'vue'
import * as THREE from 'three'

import glsl from 'glslify'

// eslint-disable-next-line
let OrbitControls = require('three-orbit-controls')(THREE)

export default {
  name: 'Webgl',
  setup() {
    const container = ref()

    let time = 0
    let camera, scene, renderer
    let sketchGeometry, sketchMaterial, sketchMesh

    const setSize = () => {
      const { width, height } = container.value.getBoundingClientRect()

      renderer.setSize(width, height)
    }

    const setCameraAspect = () => {
      const { width, height } = container.value.getBoundingClientRect()

      camera.aspect = width / height
      camera.updateProjectionMatrix()
    }

    const createSketch = (addToScene = true) => {
      sketchGeometry = new THREE.PlaneBufferGeometry(1.0, 1.0, 1.0, 1.0)

      sketchMaterial = new THREE.MeshNormalMaterial({
        side: THREE.DoubleSide,
      })

      sketchMesh = new THREE.Mesh(sketchGeometry, sketchMaterial)

      addToScene && scene.add(sketchMesh)
    }

    const createCamera = () => {
      const { width, height } = container.value.getBoundingClientRect()

      camera = new THREE.PerspectiveCamera(70, width / height, 0.001, 100)
      camera.position.z = 2
    }

    const createScene = () => {
      scene = new THREE.Scene()
    }

    const createRenderer = () => {
      const { width, height } = container.value.getBoundingClientRect()

      renderer = new THREE.WebGLRenderer({ antialias: false })
    }

    const createOrbitControls = () => {
      new OrbitControls(camera, renderer.domElement)
    }

    const update = () => {
      renderer.render(scene, camera)
      time += 1

      window.requestAnimationFrame(update)
    }

    const viewportHandler = () => {
      setSize()
      setCameraAspect()
    }

    onMounted(() => {
      createCamera()
      createScene()

      createSketch()

      createRenderer()
      createOrbitControls()

      setSize()
      container.value.appendChild(renderer.domElement)

      update()

      window.addEventListener('resize', viewportHandler)
    })

    onUnmounted(() => {
      window.removeEventListener('resize', viewportHandler)
    })

    return {
      container,
    }
  },
}
</script>
