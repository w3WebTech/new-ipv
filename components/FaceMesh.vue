<template>
    <div class="relative">
      <video v-if="!isImageCaptured" ref="video" class="w-full h-auto" autoplay playsinline></video>
      <canvas v-if="!isImageCaptured" ref="canvas" class="absolute top-0 left-0 w-full h-auto"></canvas>
      <div v-if="!isImageCaptured" class="absolute top-0 left-0 w-full h-full flex-col items-center justify-center bg-black bg-opacity-50">
        <p v-if="!isFaceDetected" class="text-white text-lg">Please position your face correctly in the frame.</p>
        <p v-if="isFaceDetected && !isFaceStraight" class="text-white text-lg">Please look straight at the camera.</p>
        <p v-if="isFaceDetected && isFaceTooClose" class="text-white text-lg">Please move slightly away from the camera.</p>
        <p v-if="isFaceDetected && isFaceTooFar" class="text-white text-lg">Please move closer to the camera.</p>
        <p v-if="isFaceDetected && isEyesClosed" class="text-white text-lg">Please keep your eyes open.</p>
        <p v-if="isFaceDetected && isMultipleFaces" class="text-white text-lg">Only one face should be in the frame.</p>
      </div>
      <div v-if="isImageCaptured" class="mt-4">
        <img :src="capturedImage" alt="Captured Image" class="w-full h-auto" />
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
  const capturedImage = ref(null);
  const isFaceDetected = ref(false);
  const isImageCaptured = ref(false);
  const isFaceStraight = ref(false);
  const isFaceTooClose = ref(false);
  const isFaceTooFar = ref(false);
  const isEyesClosed = ref(false);
  const isMultipleFaces = ref(false);
  
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
      console.log('FaceMesh results:', results); // Debugging: Log results
  
      const ctx = canvas.value.getContext('2d');
      const width = video.value.videoWidth;
      const height = video.value.videoHeight;
  
      // Set canvas dimensions to match video feed
      canvas.value.width = width;
      canvas.value.height = height;
  
      ctx.clearRect(0, 0, width, height);
  
      if (results.multiFaceLandmarks) {
        isFaceDetected.value = true; // Face detected
  
        // Check for multiple faces
        if (results.multiFaceLandmarks.length > 1) {
          isMultipleFaces.value = true;
          return;
        } else {
          isMultipleFaces.value = false;
        }
  
        const landmarks = results.multiFaceLandmarks[0];
  
        // Draw landmarks on the canvas
        drawConnectors(ctx, landmarks, FACEMESH_TESSELATION, {
          color: '#C0C0C0',
          lineWidth: 1,
        });
  
        landmarks.forEach((landmark) => {
          const x = landmark.x * width;
          const y = landmark.y * height;
          ctx.beginPath();
          ctx.arc(x, y, 2, 0, 2 * Math.PI);
          ctx.fillStyle = 'red';
          ctx.fill();
        });
  
        // Check face alignment (looking straight)
        const noseTip = landmarks[1]; // Nose tip landmark
        const leftEye = landmarks[33]; // Left eye landmark
        const rightEye = landmarks[263]; // Right eye landmark
        const chin = landmarks[152]; // Chin landmark
  
        // Calculate face angle using nose tip and chin
        const faceAngle = Math.atan2(chin.y - noseTip.y, chin.x - noseTip.x) * (180 / Math.PI);
  
        // Adjust threshold for face alignment (more lenient)
        if (Math.abs(faceAngle) > 15) { // Increased threshold from 10 to 15
          isFaceStraight.value = false;
        } else {
          isFaceStraight.value = true;
        }
  
        // Check face distance (too close or too far)
        const faceWidth = Math.abs(leftEye.x - rightEye.x) * width;
        if (faceWidth > 300) {
          isFaceTooClose.value = true;
          isFaceTooFar.value = false;
        } else if (faceWidth < 150) {
          isFaceTooFar.value = true;
          isFaceTooClose.value = false;
        } else {
          isFaceTooClose.value = false;
          isFaceTooFar.value = false;
        }
  
        // Check if eyes are closed
        const leftEyeTop = landmarks[159]; // Top of left eye
        const leftEyeBottom = landmarks[145]; // Bottom of left eye
        const rightEyeTop = landmarks[386]; // Top of right eye
        const rightEyeBottom = landmarks[374]; // Bottom of right eye
  
        const leftEyeHeight = Math.abs(leftEyeTop.y - leftEyeBottom.y);
        const rightEyeHeight = Math.abs(rightEyeTop.y - rightEyeBottom.y);
  
        if (leftEyeHeight < 0.02 || rightEyeHeight < 0.02) {
          isEyesClosed.value = true;
        } else {
          isEyesClosed.value = false;
        }
  
        // Log conditions for debugging
        console.log({
          isFaceStraight: isFaceStraight.value,
          isFaceTooClose: isFaceTooClose.value,
          isFaceTooFar: isFaceTooFar.value,
          isEyesClosed: isEyesClosed.value,
          isMultipleFaces: isMultipleFaces.value,
        });
  
        // Capture image only if all conditions are met
        if (
          
          !isFaceTooClose.value &&
          !isFaceTooFar.value &&
          !isEyesClosed.value &&
          !isMultipleFaces.value
        ) {
          console.log('All conditions met. Capturing image...'); // Debugging
          captureImage();
        }
      } else {
        isFaceDetected.value = false; // No face detected
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
    isImageCaptured.value = true; // Stop showing live camera feed
  };
  </script>
  
  <style scoped>
  /* Add custom styles if needed */
  .relative {
    position: relative;
  }
  .absolute {
    position: absolute;
  }
  </style>