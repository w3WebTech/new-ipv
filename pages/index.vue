<template>
  <div class="card flex justify-center px-5 my-10">
    <Stepper value="1" class="basis-[50rem]">
      <StepList>
        <Step value="1" class="relative">Step 1</Step>
        <Step value="2" class="relative">Step 2</Step>
        <Step value="3"  class="relative">Step 3</Step>
      </StepList>
      <StepPanels>
        <!-- Step 1 Panel -->
        <StepPanel v-slot="{ activateCallback }" value="1">
          <div class="border-2 border-dashed">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 font-medium">
                <div class="card px-5 py-10">
                  <div class="gap-2">
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
                  </div>
                </div>
              </div>
              <div class="flex p-4 justify-end bg-gray-50">
                <Button label="Next" icon="pi pi-arrow-right" iconPos="right"
                  @click="validateStep1(activateCallback)" />
              </div>
            </div>
          </div>
        </StepPanel>

        <!-- Step 2 Panel -->
        <StepPanel v-slot="{ activateCallback }" value="2">
          <div class="border-2 border-dashed">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 font-medium">
                <div class="card py-10">
                  <div class="gap-2">
                    <div v-if="coordinates" class="mt-4 px-5">
                      <div class="font-bold my-2">{{ clientName }},</div>
                      <div>Your Location is <span class="font-semibold" v-if="address">{{ address }}</span></div>
                    </div>
                    <div v-else class="mt-4 text-red-500">
                      <p>Please allow GPS access to get your location.</p>
                      <Button label="Reload" @click="reloadPage" />
                    </div>
                    <div class="font-bold my-2 px-5" v-if="!capturedImage">
                      <Button label="Capture Image" class="w-full" @click="openCameraModal" />
                    </div>
                    <div v-if="capturedImage" class="mt-4">
                      <img :src="capturedImage" alt="Captured Image" class="w-full h-[300px] px-5" />
                      <div v-if="capturedImage && errorMessage" class="mt-2 text-red-500">
                        <p class="px-5">{{ errorMessage }}</p>
                      </div>
                      <div v-if="capturedImage && isFaceProcessing" class="flex justify-center mt-2">
                        <p class="text-gray-500">Please wait, processing...</p>
                      </div>
                      <div class="flex justify-center mt-2 px-5" v-if="!isFaceProcessing">
                        <Button label="Retake" @click="retakeCapture" class="w-full" />
                      </div>
                    </div>
                  </div>
                </div>
              </div>
              <div class="flex p-4 justify-between bg-gray-50">
                <Button label="Back" severity="secondary" icon="pi pi-arrow-left" @click="activateCallback('1')" />
                <Button label="Next" icon="pi pi-arrow-right" iconPos="right"
                  @click="validateStep2(activateCallback)" />
              </div>
            </div>
          </div>
        </StepPanel>

        <!-- Step 3 Panel -->
        <StepPanel v-slot="{ activateCallback }" value="3">
          <div class="border-2 border-dashed rounded-lg shadow-lg">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 flex-auto flex justify-center items-center font-medium">
                <div class="card flex flex-col items-center justify-center py-10 px-6">
                  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="green" class="h-14 w-14 m-2">
  <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75M21 12c0 1.268-.63 2.39-1.593 3.068a3.745 3.745 0 0 1-1.043 3.296 3.745 3.745 0 0 1-3.296 1.043A3.745 3.745 0 0 1 12 21c-1.268 0-2.39-.63-3.068-1.593a3.746 3.746 0 0 1-3.296-1.043 3.745 3.745 0 0 1-1.043-3.296A3.745 3.745 0 0 1 3 12c0-1.268.63-2.39 1.593-3.068a3.745 3.745 0 0 1 1.043-3.296 3.746 3.746 0 0 1 3.296-1.043A3.746 3.746 0 0 1 12 3c1.268 0 2.39.63 3.068 1.593a3.746 3.746 0 0 1 3.296 1.043 3.746 3.746 0 0 1 1.043 3.296A3.745 3.745 0 0 1 21 12Z" />
</svg>

                  <h2 class="text-xl font-bold">Thank You , {{ clientName }}!</h2>
                  <p class="text-lg text-gray-700 mt-2 flex justify-center text-center ">Your IP verification is completed.</p>
                  <div class="mt-4">
                    <Button label="Proceed to E-Sign" @click="proceedToESign"
                      class="bg-blue-600 text-white hover:bg-blue-700 transition duration-200" />
                  </div>
                </div>
              </div>
            </div>
            <div class="p-4 bg-gray-50">
              <Button label="Back" severity="secondary" icon="pi pi-arrow-left" @click="activateCallback('2')" />
            </div>
          </div>
        </StepPanel>
      </StepPanels>
    </Stepper>

    <!-- Camera Modal -->
    <Transition name="fade absolute">
      <div v-if="isCameraModalOpen"
        class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 h-screen w-screen ">
        <div class="bg-white p-5 rounded-lg shadow-lg h-[80vh] w-[90vw] flex flex-col">
          <h2 class="text-lg font-bold mb-4">Capture Image</h2>
          <div class="flex-1">
            <video ref="video" autoplay playsinline class="w-full h-full"></video>
          </div>
          <div class="flex justify-center mt-2">
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
import Step from 'primevue/step';
import StepList from 'primevue/steplist';
import StepPanel from 'primevue/steppanel';
import { useRoute } from 'vue-router';
import Toast from 'primevue/toast';
import 'primeicons/primeicons.css';
import { FaceMesh } from '@mediapipe/face_mesh';

const route = useRoute();
const clientName = ref(route.query.clientName || '');
const clientCode = ref(route.query.clientCode || '');
const coordinates = ref(null);
const capturedImage = ref(null);
const isCameraActive = ref(false);
const isCameraModalOpen = ref(false);
const address = ref(null);
const toast = useToast();
let faceMesh = null;
const errorMessage = ref('');
const isFaceProcessing = ref(false);

const getLocation = () => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      (position) => {
        coordinates.value = {
          latitude: position.coords.latitude,
          longitude: position.coords.longitude,
        };
        getAddressFromCoordinates(coordinates.value.latitude, coordinates.value.longitude);
      },
      (error) => {
        console.error('Error getting location:', error);
        toast.add({
          severity: 'error',
          summary: 'Error',
          detail: 'Please allow location access to proceed.',
          life: 3000,
        });
      },
      { timeout: 10000 } // 10 seconds timeout
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

const getAddressFromCoordinates = async (lat, lon) => {
  try {
    const response = await fetch(`https://nominatim.openstreetmap.org/reverse?lat=${lat}&lon=${lon}&format=json`);
    const data = await response.json();
    if (data && data.display_name) {
      address.value = data.display_name;
    } else {
      address.value = 'Address not found.';
    }
  } catch (error) {
    console.error('Error fetching address:', error);
    address.value = 'Error fetching address.';
  }
};

const openCameraModal = async () => {
  isCameraModalOpen.value = true;
  await startCamera();
};

const startCamera = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' } });
    const video = document.querySelector('video');
    if (video) {
      video.srcObject = stream;
      video.play();
      console.log('Camera started:', stream);
    }
  } catch (error) {
    console.error('Error accessing camera:', error);
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
    console.log('FaceMesh initialized');
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
    isFaceProcessing.value = true; // Show loader
    await faceMesh.send({ image: imageData });
  } catch (error) {
    console.error('Error capturing image:', error);
    isFaceProcessing.value = false; // Hide loader
  }
};

const onFaceMeshResults = (results) => {
  isFaceProcessing.value = false; // Hide loader

  // Check if face landmarks exist and are not empty
  if (results && results.multiFaceLandmarks && results.multiFaceLandmarks.length > 0) {
    const landmarks = results.multiFaceLandmarks[0]; // Assuming we are dealing with the first detected face
    
    // You can add more checks here if needed to validate that the landmarks are clear enough
    if (landmarks && landmarks.length > 0) {
      closeCameraModal(); // Close the camera modal
      toast.add({
        severity: 'success',
        summary: 'Success',
        detail: 'Face detected successfully!',
        life: 3000,
      });
    } else {
      errorMessage.value = 'No clear face detected. Please ensure your face is visible and retake the image.';
      toast.add({
        severity: 'error',
        summary: 'Error',
        detail: 'No clear face detected. Please ensure your face is visible and retake the image.',
        life: 3000,
      });
      closeCameraModal();
    }
  } else {
    errorMessage.value = 'No face detected. Please retake the image.';
    toast.add({
      severity: 'error',
      summary: 'Error',
      detail: 'No face detected. Please retake the image.',
      life: 3000,
    });
    closeCameraModal();
  }
};


const closeCameraModal = () => {
  isCameraModalOpen.value = false;
  const video = document.querySelector('video');
  if (video && video.srcObject) {
    const stream = video.srcObject;
    const tracks = stream.getTracks();
    tracks.forEach(track => track.stop());
    video.srcObject = null;
  }
};

const retakeCapture = () => {
  capturedImage.value = null; // Clear the captured image
  isFaceProcessing.value = false; // Hide the processing loader
  isCameraModalOpen.value = true; // Open the camera modal again
 startCamera();
};

const validateStep1 = (activateCallback) => {
  if (coordinates.value) {
    activateCallback('2');
  } else {
    toast.add({
      severity: 'error',
      summary: 'Error',
      detail: 'Please grant location access before proceeding.',
      life: 3000,
    });
  }
};

const validateStep2 = (activateCallback) => {
  if (capturedImage.value && errorMessage =='') {
    activateCallback('3');
  } else {
    toast.add({
      severity: 'error',
      summary: 'Error',
      detail: 'Please capture a face image before proceeding.',
      life: 3000,
    });
  }
};

const proceedToESign = () => {
  // Logic to proceed to e-sign
  toast.add({
    severity: 'success',
    summary: 'Proceeding to E-Sign',
    detail: 'You are now ready to proceed.',
    life: 3000,
  });
};

const reloadPage = () => {
  window.location.reload();
};
</script>


<style >
.p-step-number {
  z-index: 0 !important;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}

</style>