<template>
  <div class="card flex justify-center">
    <Stepper value="1" class="basis-[50rem]">
      <div class="font-bold text-lg text-white py-5 flex bg-[#2249A6]">
        <img src="public/logo-2.jpeg" alt="" height="90" width="90" class="mx-4 rounded">
        <div class="px-5 flex flex-col justify-center item-center text-center"> In person Verification</div>
      </div>

      <StepPanels class="px-5 my-3">
        <StepPanel v-slot="{ activateCallback }" value="1">
          <div class="border-2 border-dashed">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 font-medium">
                <div class="card px-5 py-5">
                  <div class="gap-2">
                    <div>
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

                      <!-- Show captured image if available -->
                      <div v-if="messageType.value === 'success'" class="mt-4">
                        <img :src="capturedImage" alt="Captured Image" class="w-full h-[300px] px-5" />
                      </div>

                      <!-- Show the proceed button when verification is successful -->
                      <div v-if="messageType.value === 'success'" class="flex justify-center">
                        <Button label="Proceed to E-Sign" @click="proceedToESign" class="w-full" />
                      </div>

                      <!-- If not successful, show the capture button -->
                      <div v-if="messageType.value !== 'success'" class="font-bold my-2 flex items-center justify-between">
                        <Button label="PROCEED" @click="openCameraModal" class="w-full my-3"/>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </StepPanel>
      </StepPanels>
    </Stepper>

    <Transition name="fade absolute">
      <div v-if="isCameraModalOpen" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 h-screen w-screen">
        <div class="bg-white p-5 rounded-lg shadow-lg h-[80vh] w-[90vw] flex flex-col">
          <!-- Show location coordinates -->
          <div v-if="coordinates" class="text-gray-700 my-1">
            <p><strong>Location:</strong> {{ coordinates.latitude }}, {{ coordinates.longitude }}</p>
          </div>

          <!-- Show video feed when verification is not completed -->
          <div v-if="messageType.value !== 'success'" class="flex-1">
            <video ref="video" autoplay playsinline class="w-full h-full"></video>
          </div>

          <!-- Show error or success message -->
          <div v-if="errorMessage" class="my-1" :class="messageTypeClass">
            <p>{{ errorMessage }}</p>
          </div>

          <!-- If verification is successful, show the 'Proceed to E-Sign' button -->
          <div v-if="messageType.value === 'success'" class="flex justify-center my-1">
            <Button label="Proceed to E-Sign" @click="proceedToESign" class="w-full" />
          </div>

          <!-- If verification is not successful, show the capture image button -->
          <div v-if="messageType.value !== 'success'" class="flex justify-center my-1">
            <Button label="Capture Image" @click="captureImage" class="w-full" />
          </div>
        </div>
      </div>
    </Transition>

    <Toast />
  </div>
</template>
<script setup>
import { ref, onMounted, computed } from 'vue';
import { useToast } from 'primevue/usetoast';
import Button from 'primevue/button';
import Stepper from 'primevue/stepper';
import StepPanel from 'primevue/steppanel';
import Toast from 'primevue/toast';
import { FaceMesh } from '@mediapipe/face_mesh';
import 'primeicons/primeicons.css';

const toast = useToast();
const clientName = ref("Client Name");  // Placeholder
const clientCode = ref("Client Code");  // Placeholder
const coordinates = ref(null);
const capturedImage = ref(null);
const isCameraModalOpen = ref(false);
const errorMessage = ref('');
const messageType = ref(''); // Set as ref to trigger reactivity

// Computed property for conditional styling based on message type
const messageTypeClass = computed(() => {
  return {
    'flex justify-center text-sm bg-[#E4AEAE] rounded-md p-2': messageType.value === 'error',
    'flex justify-center bg-[#AEE3CF] text-sm rounded-md p-2': messageType.value === 'success',
  };
});

const getLocation = () => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      (position) => {
        coordinates.value = {
          latitude: position.coords.latitude,
          longitude: position.coords.longitude,
        };
      },
      (error) => {
        toast.add({
          severity: 'error',
          summary: 'Error',
          detail: 'Please allow location access to proceed.',
          life: 3000,
        });
      },
      { timeout: 10000 }
    );
  } else {
    toast.add({
      severity: 'error',
      summary: 'Error',
      detail: 'Geolocation is not supported by this browser.',
      life: 3000,
    });
  }
};

const checkCameraPermission = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    const tracks = stream.getTracks();
    tracks.forEach(track => track.stop());
    return true;
  } catch (error) {
    console.error('Camera access denied:', error);
    return false;
  }
};

const openCameraModal = async () => {
  const hasPermission = await checkCameraPermission();
  if (hasPermission) {
    isCameraModalOpen.value = true;
    await startCamera();
  } else {
    toast.add({
      severity: 'error',
      summary: 'Error',
      detail: 'Camera access is required to capture an image. Please grant camera access.',
      life: 3000,
    });
  }
};

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

onMounted(async () => {
  if (typeof window !== 'undefined') {
    const faceMesh = new FaceMesh({
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

const captureImage = async () => {
  const video = document.querySelector('video');
  const canvas = document.createElement('canvas');
  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;
  const context = canvas.getContext('2d');
  context.drawImage(video, 0, 0, canvas.width, canvas.height);
  capturedImage.value = canvas.toDataURL();

  try {
    const imageData = context.getImageData(0, 0, canvas.width, canvas.height);
    await faceMesh.send({ image: imageData });
  } catch (error) {
    console.error('Error capturing image:', error);
  }
};

const onFaceMeshResults = (results) => {
  if (results && results.multiFaceLandmarks && results.multiFaceLandmarks.length > 0) {
    errorMessage.value = 'Verification Completed';
    messageType.value = 'success';
  } else {
    errorMessage.value = 'No face detected. Please retake the image.';
    capturedImage.value = null;
    messageType.value = 'error';
  }
};

const proceedToESign = () => {
  console.log("Proceeding to E-Sign");
};
</script>
<style>
.p-button {
 
    color: white !important;
    background:#2249A6 !important;
    border: 1px solid #2249A6 !important;}
    </style>