<template>
    <div class="card flex justify-center">
      <Stepper value="1" class="basis-[50rem]">
        <div class="font-bold text-lg text-white py-5 flex bg-[#10B981]">
          <img src="public/logo-2.jpeg" alt="" height="50" width="50" class="mx-5 rounded">
          <div class="px-5 my-3"> In person Verification</div>
        </div>
  
        <StepPanels class="px-5 my-5">
          <!-- Step 1 Panel -->
          <StepPanel v-slot="{ activateCallback }" value="1">
            <div class="border-2 border-dashed">
              <div class="flex flex-col h-full">
                <div class="border-gray-200 rounded bg-gray-50 font-medium">
                  <div class="card px-5 py-10">
                    <div class="gap-2">
                      <!-- Check if captured image is available -->
                      <div v-if="capturedImage" class="card flex flex-col items-center justify-center py-10 px-6">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="green" class="h-14 w-14 m-2">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75M21 12c0 1.268-.63 2.39-1.593 3.068a3.745 3.745 0 0 1-1.043 3.296 3.745 3.745 0 0 1-3.296 1.043A3.745 3.745 0 0 1 12 21c-1.268 0-2.39-.63-3.068-1.593a3.746 3.746 0 0 1-3.296-1.043 3.745 3.745 0 0 1-1.043-3.296A3.745 3.745 0 0 1 3 12c0-1.268.63-2.39 1.593-3.068a3.745 3.745 0 0 1 1.043-3.296 3.746 3.746 0 0 1 3.296-1.043A3.746 3.746 0 0 1 12 3c1.268 0 2.39.63 3.068 1.593a3.746 3.746 0 0 1 3.296 1.043 3.746 3.746 0 0 1 1.043 3.296A3.745 3.745 0 0 1 21 12Z" />
                        </svg>
  
                        <!-- Thank You message with Client Name -->
                        <h2 class="text-xl font-bold">Thank You, {{ clientName }}!</h2>
                        <p class="text-lg text-gray-700 mt-2 flex justify-center text-center">Your IP verification is completed.</p>
                        <div class="mt-4">
                          <Button label="Proceed to E-Sign" @click="proceedToESign"
                            class="bg-blue-600 text-white hover:bg-blue-700 transition duration-200" />
                        </div>
                      </div>
  
                      <!-- Display Step UI if no captured image -->
                      <div v-else>
                        <div class="font-bold my-5">Hello, {{ clientName }} ({{ clientCode }})</div>
                        <div class="font-bold flex justify-center">How to do</div>
                        <div class="font-bold my-2 flex items-center justify-between">
                          <div>1. Enable GPS</div>
                          <Button icon="pi pi-map-marker" aria-label="Save" @click="getLocation" />
                        </div>
                        <div class="font-bold my-2 flex items-center justify-between">
                          <div>2. Allow Camera</div>
                          <Button icon="pi pi-camera" aria-label="Save" @click="startCamera" />
                        </div>
  
                        <!-- Show captured image in Step 1 if it's available -->
                        <div v-if="capturedImage" class="mt-4">
                          <img :src="capturedImage" alt="Captured Image" class="w-full h-[300px] px-5" />
                          <div v-if="errorMessage" class="mt-2 text-red-500">
                            <p>{{ errorMessage }}</p>
                          </div>
                        </div>
  
                        <div class="font-bold my-2 flex items-center justify-between">
                          <Button icon="pi pi-camera" aria-label="Save" label="Capture Image" @click="openCameraModal" />
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </StepPanel>
  
          <!-- Step 2 and 3 panels would remain unchanged -->
        </StepPanels>
      </Stepper>
  
      <!-- Camera Modal -->
      <Transition name="fade absolute">
        <div v-if="isCameraModalOpen" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 h-screen w-screen">
          <div class="bg-white p-5 rounded-lg shadow-lg h-[80vh] w-[90vw] flex flex-col">
            <h2 class="text-lg font-bold mb-4">Capture Image</h2>
            <div class="flex-1">
              <video ref="video" autoplay playsinline class="w-full h-full"></video>
            </div>
            <div class="flex justify-center mt-2">
              <Button label="Capture Image" @click="captureImage" class="w-full" />
            </div>
            <div v-if="errorMessage" class="mt-2 text-red-500">
              <p>{{ errorMessage }}</p>
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
  const clientName = ref("Client Name");  // Placeholder
  const clientCode = ref("Client Code");  // Placeholder
  const coordinates = ref(null);
  const capturedImage = ref(null);
  const isCameraModalOpen = ref(false);
  const errorMessage = ref('');
  let faceMesh = null;
  
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
        { timeout: 10000 }  // 10 seconds timeout
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
  
  const captureImage = async () => {
    if (!faceMesh) {
      console.error('FaceMesh is not initialized.');
      return;
    }
  
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
      errorMessage.value = '';
      isCameraModalOpen.value = false;   // Reset error message on success
    } else {
      errorMessage.value = 'No face detected. Please retake the image.';
      capturedImage.value = null;  // Clear the captured image on error
      toast.add({
        severity: 'error',
        summary: 'Error',
        detail: 'No face detected. Please retake the image.',
        life: 3000,
      });
    }
  };
  </script>
  