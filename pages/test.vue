<template>
  <div class="card flex justify-center">
    <!-- Stepper UI -->
    <Stepper value="1" class="basis-[50rem]">
      <div class="font-bold text-lg text-white py-5 flex bg-[#2249A6]">
        <img src="public/logo-2.jpeg" alt="" height="90" width="90" class="mx-4 rounded">
        <div class="px-5 flex flex-col justify-center item-center text-center"> In-person Verification</div>
      </div>

      <StepPanels class="px-5 my-3">
        <!-- Step 1 Panel -->
        <StepPanel v-slot="{ activateCallback }" value="1">
          <div class="border-2 border-dashed">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 font-medium">
                <div class="card px-5 py-5">
                  <div class="gap-2">
                    <!-- Display User Info -->
                    <div class="font-bold my-5">Hello, {{ clientName }} ({{ clientCode }})</div>
                    <div class="font-bold flex justify-start">How to do IPV</div>
                    <div class="font-bold my-2 flex items-center justify-between">
                      <div>1. Enable GPS</div>
                      <Button icon="pi pi-map-marker" aria-label="Save" @click="getLocation" />
                    </div>
                    <div class="font-bold my-2 flex items-center justify-between">
                      <div>2. Allow Camera</div>
                      <Button icon="pi pi-camera" aria-label="Save" @click="startCamera" />
                    </div>

                    <!-- Proceed Button to open Modal -->
                    <div class="font-bold my-2 flex items-center justify-between">
                      <Button label="PROCEED" @click="openCameraModal" class="w-full my-3"/>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </StepPanel>
      </StepPanels>
    </Stepper>

    <!-- Camera Modal -->
    <Transition name="fade absolute">
      <div v-if="isCameraModalOpen" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 h-screen w-screen">
        <div class="bg-white p-5 rounded-lg shadow-lg h-[80vh] w-[90vw] flex flex-col">
          <div v-if="coordinates" class="text-gray-700 my-1">
            <p><strong>Location:</strong> {{ coordinates.latitude }}, {{ coordinates.longitude }}</p>
          </div>

          <!-- Video feed when not yet captured -->
          <div v-if="!capturedImage" class="flex-1 relative">
            <video ref="video" autoplay playsinline class="w-full h-full object-cover"></video>
          </div>

          <!-- Show captured image when verification is successful -->
          <div v-if="capturedImage && isValidImage" class="mt-4 relative">
            <img :src="capturedImage" alt="Captured Image" class="w-full h-full object-cover" />
            <p class="text-green-500 mt-2">Image successfully captured!</p>
          </div>

          <!-- Show error message if the image is invalid -->
          <div v-if="!isValidImage && !capturedImage" class="text-red-500 mt-2">
            <p>Error: Invalid Image! Please retake.</p>
          </div>

          <!-- Proceed to E-Sign Button if success -->
          <div v-if="isValidImage" class="flex justify-center my-1">
            <Button label="Proceed to E-Sign" @click="proceedToESign" class="w-full" />
          </div>

          <!-- Capture Image Button -->
          <div v-if="!capturedImage" class="font-bold my-1 flex items-center justify-between">
            <Button label="Capture Image" @click="captureImage" class="w-full" />
          </div>
        </div>
      </div>
    </Transition>

    <Toast />
  </div>
</template>




<script setup>
import { ref, onMounted } from 'vue';
import { useToast } from 'primevue/usetoast';
import Button from 'primevue/button';
import Stepper from 'primevue/stepper';
import StepPanel from 'primevue/steppanel';
import Toast from 'primevue/toast';
import { FaceMesh } from '@mediapipe/face_mesh';

const toast = useToast();
const clientName = ref("Client Name");
const clientCode = ref("Client Code");
const coordinates = ref(null);
const capturedImage = ref(null);
const isCameraModalOpen = ref(false);
const isValidImage = ref(false); // State for image validation
let faceMesh = null;

// Initialize face mesh for face detection
onMounted(() => {
  if (typeof window !== 'undefined') {
    faceMesh = new FaceMesh({
      locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh/${file}`,
    });

    faceMesh.setOptions({
      maxNumFaces: 1,
      refineLandmarks: true,
      minDetectionConfidence: 0.5,
      minTrackingConfidence: 0.5,
    });

    faceMesh.onResults(onFaceMeshResults);
  }
});

// Open camera modal and check permission
const openCameraModal = async () => {
  const hasPermission = await checkCameraPermission();
  if (hasPermission) {
    isCameraModalOpen.value = true;
    await startCamera();
  } else {
    toast.add({
      severity: 'error',
      summary: 'Error',
      detail: 'Camera access is required to capture an image.',
      life: 3000,
    });
  }
};

// Check camera permission
const checkCameraPermission = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    const tracks = stream.getTracks();
    tracks.forEach(track => track.stop());
    return true;
  } catch (error) {
    return false;
  }
};

// Start video feed from the camera
const startCamera = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' } });
    const video = document.querySelector('video');
    if (video) {
      video.srcObject = stream;
      video.play();
    }
  } catch (error) {
    toast.add({
      severity: 'error',
      summary: 'Error',
      detail: 'Please allow camera access to proceed.',
      life: 3000,
    });
  }
};

// Capture the image from video feed
const captureImage = async () => {
  const video = document.querySelector('video');
  if (!video) return;

  const canvas = document.createElement('canvas');
  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;
  const context = canvas.getContext('2d');
  context.drawImage(video, 0, 0, canvas.width, canvas.height);
  capturedImage.value = canvas.toDataURL();
  
  const imageData = context.getImageData(0, 0, canvas.width, canvas.height);
  await faceMesh.send({ image: imageData });
};

// FaceMesh result processing (image validation)
const onFaceMeshResults = (results) => {
  if (results && results.multiFaceLandmarks && results.multiFaceLandmarks.length > 0) {
    isValidImage.value = true;  // Image is valid
  } else {
    isValidImage.value = false;  // Invalid image
  }
};

// Proceed to E-Sign functionality (placeholder)
const proceedToESign = () => {
  console.log('Proceeding to E-Sign...');
};
</script>



<style>
.p-button {
 
    color: white !important;
    background:#2249A6 !important;
    border: 1px solid #2249A6 !important;}
    </style>
