<template>
  <div ref="container"></div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import * as THREE from 'three'

const container = ref(null)

onMounted(() => {
  const scene = new THREE.Scene()
  const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
  const renderer = new THREE.WebGLRenderer()
  
  renderer.setSize(window.innerWidth, window.innerHeight)
  container.value.appendChild(renderer.domElement)

  const geometry = new THREE.BufferGeometry()
  const vertices = new Float32Array([
    0.0, 1.0, 0.0,
    -1.0, -1.0, 0.0,
    1.0, -1.0, 0.0
  ])
  geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3))
  
  const material = new THREE.MeshBasicMaterial({ 
    color: 0x00ff00,
    side: THREE.DoubleSide 
  })
  
  const triangle = new THREE.Mesh(geometry, material)
  scene.add(triangle)
  
  camera.position.z = 5

  function animate() {
    requestAnimationFrame(animate)
    triangle.rotation.z += 0.01
    renderer.render(scene, camera)
  }
  
  animate()
})
</script>

<style scoped>
div {
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
}
</style>
