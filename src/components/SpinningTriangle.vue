<template>
  <div ref="container"></div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import * as THREE from 'three'

const container = ref(null)

onMounted(() => {
  // Basic Scene Setup
  const scene = new THREE.Scene()
  const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
  )
  camera.position.z = 5

  const renderer = new THREE.WebGLRenderer()
  renderer.setSize(window.innerWidth, window.innerHeight)
  container.value.appendChild(renderer.domElement)

  // Box (2x2x2) with Edges
  const boxGeometry = new THREE.BoxGeometry(2, 2, 2)
  const edgesGeometry = new THREE.EdgesGeometry(boxGeometry)
  const edgesMaterial = new THREE.LineBasicMaterial({ color: 0x00ff00 })
  const box = new THREE.LineSegments(edgesGeometry, edgesMaterial)
  scene.add(box)

  // Sphere (radius 0.2, just for a quicker bounce range)
  const sphereGeometry = new THREE.SphereGeometry(0.2, 16, 16)
  const sphereMaterial = new THREE.MeshBasicMaterial({ color: 0xff0000 })
  const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial)
  scene.add(sphere)

  // Half-extent for the box is 1 (since box is 2x2x2)
  // The sphere's center can range from -1+radius to +1-radius.
  const halfExtent = 1
  const sphereRadius = 0.2

  // Initial sphere position & velocity
  const spherePos = new THREE.Vector3(0, 0, 0)
  // Pick a velocity large enough to see immediate movement
  const sphereVel = new THREE.Vector3(0.02, 0.01, 0.015)

  // Simple update ignoring cube's rotation for collisions
  function updateSpherePosition() {
    // Move the sphere
    spherePos.add(sphereVel)

    // Check collision along x, y, z
    for (const axis of ['x', 'y', 'z']) {
      if (spherePos[axis] < -halfExtent + sphereRadius) {
        spherePos[axis] = -halfExtent + sphereRadius
        sphereVel[axis] *= -1 // bounce
      } else if (spherePos[axis] > halfExtent - sphereRadius) {
        spherePos[axis] = halfExtent - sphereRadius
        sphereVel[axis] *= -1 // bounce
      }
    }

    // Update sphere's position in the scene
    sphere.position.copy(spherePos)
  }

  // Animation Loop
  function animate() {
    requestAnimationFrame(animate)

    // Optionally spin the box just for visuals
    box.rotation.x += 0.01
    box.rotation.y += 0.01

    // Update sphere
    updateSpherePosition()

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