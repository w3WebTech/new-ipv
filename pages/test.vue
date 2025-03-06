<template>
  <div class="card flex justify-center">
    <Stepper value="1" class="basis-[50rem]">
      <div class="font-bold text-lg text-white py-5 flex bg-[#2249A6]">
        <img src="public/logo-2.jpeg" alt="" height="90" width="90" class="mx-4 rounded">
        <div class="px-5  flex flex-col justify-center item-center text-center"> In person Verification</div>
      </div>

      <StepPanels class="px-5 my-3">
        <!-- Step 1 Panel -->
        <StepPanel v-slot="{ activateCallback }" value="1">
          <div class="border-2 border-dashed">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 font-medium">
                <div class="card px-5 py-5">
                  <div class="gap-2">
                    <!-- Check if captured image is available -->
                    <!-- <div v-if="capturedImage" class="card flex flex-col items-center justify-center py-2 ">
                      
                      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="green" class="h-14 w-14 m-2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75M21 12c0 1.268-.63 2.39-1.593 3.068a3.745 3.745 0 0 1-1.043 3.296 3.745 3.745 0 0 1-3.296 1.043A3.745 3.745 0 0 1 12 21c-1.268 0-2.39-.63-3.068-1.593a3.746 3.746 0 0 1-3.296-1.043 3.745 3.745 0 0 1-1.043-3.296A3.745 3.745 0 0 1 3 12c0-1.268.63-2.39 1.593-3.068a3.745 3.745 0 0 1 1.043-3.296 3.746 3.746 0 0 1 3.296-1.043A3.746 3.746 0 0 1 12 3c1.268 0 2.39.63 3.068 1.593a3.746 3.746 0 0 1 3.296 1.043 3.746 3.746 0 0 1 1.043 3.296A3.745 3.745 0 0 1 21 12Z" />
                      </svg>

                    
                      <h2 class="text-xl font-bold">Thank You, {{ clientName }}!</h2>
                      <p class="text-lg text-gray-700 mt-2 flex justify-center text-center">Your IP verification is completed.</p>
                      <div v-if="capturedImage" class="mt-4">
                        <img :src="capturedImage" alt="Captured Image" class="w-full h-[300px] px-5" />
                        <div v-if="errorMessage" class="mt-2 text-red-500">
                          <p>{{ errorMessage }}</p>
                        </div>
                      </div>
                      <div class="mt-4">
                        <Button label="Proceed to E-Sign" @click="proceedToESign"
                          class="bg-blue-600 text-white hover:bg-blue-700 transition duration-200" />
                      </div>
                    </div> -->

                    <!-- Display Step UI if no captured image -->
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

                      <!-- Show captured image in Step 1 if it's available -->


                      <div class="font-bold my-2 flex items-center justify-between ">
                        <Button aria-label="Save" label="PROCEED" @click="openCameraModal" class="w-full my-3" />
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


    <Transition name="fade absolute">
      <div v-if="isCameraModalOpen"
        class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 h-screen w-screen">
        <div class="bg-white p-5 rounded-lg shadow-lg h-[80vh] w-[90vw] flex flex-col">
          <!-- <h2 class="text-lg font-bold ">Capture Image</h2> -->
          <div v-if="coordinates" class=" text-gray-700 my-1">
            <p><strong>Location:</strong> {{ coordinates.latitude }}, {{ coordinates.longitude }}</p>
          </div>



          <!-- Show video feed when verification is not completed -->

          <div>
            <div v-if="successMsg" class="mt-4">
              <img :src="capturedImage" alt="Captured Image" class="w-full h-[300px] px-5" />
            </div>
            <div v-else class="flex-1">
              <video ref="video" autoplay playsinline class="w-full h-full"></video>
            </div>
          </div>


          <div v-if="errorMessage" class="my-1" :class="messageTypeClass">
            <p>{{ errorMessage }}</p>
          </div>
          <div>
            <div v-if="successMsg" class="flex justify-center my-1">
              <Button label="Proceed to E-Sign" @click="proceedToESign" class="w-full" />
            </div>

            <!-- Capture Image button if verification is not successful -->
            <div v-else class="font-bold my-1 flex items-center justify-between">
              <Button label="Capture Image" @click="captureImage" class="w-full " />
            </div>
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

let faceMesh = null;
const messageType = ref('');
const errorMsg = ref(false);
const successMsg = ref(false)


const messageTypeClass = computed(() => {
  return {
    'flex justify-center  text-sm bg-[#E4AEAE]  rounded-md p-2': messageType.value === 'error', // red color for error
    'flex justify-center bg-[#AEE3CF]  text-sm  rounded-md p-2': messageType.value === 'success', // green color for success
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
watch(messageType, (newValue, oldValue) => {
  messageType.value = newValue
});
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
  const video = document.querySelector('video');
  if (!video) {
    console.error('Video element not found.');
    return;
  }

  const canvas = document.createElement('canvas');
  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;
  const context = canvas.getContext('2d');
  context.drawImage(video, 0, 0, canvas.width, canvas.height);
  capturedImage.value = canvas.toDataURL();

  try {
    const imageData = context.getImageData(0, 0, canvas.width, canvas.height);
    await faceMesh.send({ image: imageData }); // Use faceMesh.value
  } catch (error) {
    console.error('Error capturing image:', error);
  }
};

const onFaceMeshResults = (results) => {
  if (results && results.multiFaceLandmarks && results.multiFaceLandmarks.length > 0) {
    errorMessage.value = 'Verification Completed';
    messageType.value = 'success';
    successMsg.value = true;

  } else {
    errorMessage.value = 'Sorry ,Invalid picture ,please retake !';
    capturedImage.value = null;
    errorMsg.value = true;
    messageType.value = 'error'; // Clear the captured image on error

  }
};
</script>
<style>
.p-button {

  color: white !important;
  background: #2249A6 !important;
  border: 1px solid #2249A6 !important;
}
</style>
