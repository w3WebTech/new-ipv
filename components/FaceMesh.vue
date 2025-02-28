<template>
    <div class="relative">
      <video ref="video" class="w-full h-auto" autoplay playsinline></video>
      <canvas ref="canvas" class="absolute top-0 left-0 w-full h-auto"></canvas>
      <button @click="captureImage" class="mt-4 px-4 py-2 bg-blue-500 text-white rounded">
        Capture Image
      </button>
      <div v-if="capturedImage" class="mt-4">
        <img :src="capturedImage" alt="Captured Image" class="w-full h-auto" />
        <canvas ref="capturedCanvas" class="absolute top-0 left-0 w-full h-auto"></canvas>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import { FaceMesh, FACEMESH_TESSELATION } from '@mediapipe/face_mesh';
  import { Camera } from '@mediapipe/camera_utils';
  import { drawConnectors } from '@mediapipe/drawing_utils';
  
  const video = ref(null);
  const canvas = ref(null);
  const capturedCanvas = ref(null);
  const capturedImage = ref(null);
  
  let faceMesh;
  
  onMounted(() => {
    faceMesh = new FaceMesh({
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
      const width = video.value.videoWidth;
      const height = video.value.videoHeight;
  
      // Set canvas dimensions to match video feed
      canvas.value.width = width;
      canvas.value.height = height;
  
      ctx.clearRect(0, 0, width, height);
  
      if (results.multiFaceLandmarks) {
        for (const landmarks of results.multiFaceLandmarks) {
          drawConnectors(ctx, landmarks, FACEMESH_TESSELATION, {
            color: '#C0C0C0',
            lineWidth: 1,
          });
  
          // Draw landmarks on the canvas
          landmarks.forEach((landmark) => {
            const x = landmark.x * width;
            const y = landmark.y * height;
            ctx.beginPath();
            ctx.arc(x, y, 2, 0, 2 * Math.PI);
            ctx.fillStyle = 'red';
            ctx.fill();
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
  
  const captureImage = () => {
    const width = video.value.videoWidth;
    const height = video.value.videoHeight;
  
    // Create a temporary canvas to capture the image
    const tempCanvas = document.createElement('canvas');
    tempCanvas.width = width;
    tempCanvas.height = height;
    const tempCtx = tempCanvas.getContext('2d');
  
    // Draw the current video frame onto the temporary canvas
    tempCtx.drawImage(video.value, 0, 0, width, height);
  
    // Convert the canvas to a data URL and set it as the captured image
    capturedImage.value = tempCanvas.toDataURL('image/png');
  
    // Draw landmarks on the captured image
    const capturedCtx = capturedCanvas.value.getContext('2d');
    capturedCanvas.value.width = width;
    capturedCanvas.value.height = height;
    capturedCtx.drawImage(tempCanvas, 0, 0, width, height);
  
    // Redraw landmarks on the captured image
    faceMesh.onResults((results) => {
      if (results.multiFaceLandmarks) {
        for (const landmarks of results.multiFaceLandmarks) {
          drawConnectors(capturedCtx, landmarks, FACEMESH_TESSELATION, {
            color: '#C0C0C0',
            lineWidth: 1,
          });
  
          landmarks.forEach((landmark) => {
            const x = landmark.x * width;
            const y = landmark.y * height;
            capturedCtx.beginPath();
            capturedCtx.arc(x, y, 2, 0, 2 * Math.PI);
            capturedCtx.fillStyle = 'red';
            capturedCtx.fill();
          });
        }
      }
    });
  };
  </script>
  
  <style scoped>
  /* Add custom styles if needed */
  </style>