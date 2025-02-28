<template>
    <div class="relative">
      <video ref="video" class="w-full h-auto" autoplay playsinline></video>
      <canvas ref="canvas" class="absolute top-0 left-0 w-full h-auto"></canvas>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import { FaceMesh, FACEMESH_TESSELATION } from '@mediapipe/face_mesh';
  import { Camera } from '@mediapipe/camera_utils';
  import { drawConnectors } from '@mediapipe/drawing_utils';
  
  const video = ref(null);
  const canvas = ref(null);
  
  onMounted(() => {
    const faceMesh = new FaceMesh({
      locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh/${file}`,
    });
  
    faceMesh.setOptions({
      maxNumFaces: 1,
      refineLandmarks: true,
      minDetectionConfidence: 0.5,
      minTrackingConfidence: 0.5,
    });
  
    faceMesh.onResults((results) => {
      const ctx = canvas.value.getContext('2d');
      ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
  
      if (results.multiFaceLandmarks) {
        for (const landmarks of results.multiFaceLandmarks) {
          drawConnectors(ctx, landmarks, FACEMESH_TESSELATION, {
            color: '#C0C0C0',
            lineWidth: 1,
          });
        }
      }
    });
  
    const camera = new Camera(video.value, {
      onFrame: async () => {
        await faceMesh.send({ image: video.value });
      },
      width: 640,
      height: 480,
    });
  
    camera.start();
  });
  </script>
  
  <style scoped>
  /* Add custom styles if needed */
  </style>