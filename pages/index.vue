<template>
  <div class="card flex justify-center px-5 my-10">
    <Stepper value="1" class="basis-[50rem]">
      <StepList>
        <Step value="1">Step 1</Step>
        <Step value="2">Step 2</Step>
        <Step value="3">Step 3</Step>
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
                      <div class="">Your Location is  <span class="font-semibold" v-if="address">{{ address }}</span> </div>
                    
                     
                    </div>
                    <div v-else class="mt-4 text-red-500">
                      <p>Please allow GPS access to get your location.</p>
                      <Button label="Reload" @click="reloadPage" />
                    </div>
                    <div class="font-bold my-2 px-5" v-if="!capturedImage">
                      <Button label="Capture Image" @click="openCameraModal" class="w-full" />
                    </div>
                    <div v-if="capturedImage" class="mt-4">
                      <img :src="capturedImage" alt="Captured Image" class="w-full h-[300px] px-5" />
                      <div class="flex justify-center mt-2 px-5">
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
                  <h2 class="text-xl font-bold ">Thank You, {{ clientName }}!</h2>
                  <p class="text-lg text-gray-700 mt-2">Your IP verification is completed.</p>
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
    <Transition name="fade">
      <div v-if="isCameraModalOpen" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 h-screen w-screen">
        <div class="bg-white p-5 rounded-lg shadow-lg h-[80vh] w-[90vw] flex flex-col">
          <h2 class="text-lg font-bold mb-4">Capture Image</h2>
          <div class="flex-1">
            <video ref="video" autoplay playsinline class="w-full h-full"></video>
          </div>
          <div class="flex justify-center mt-2">
            <Button label="Capture Image" @click="captureImage" class="w-full" />
          </div>
          <!-- <div class="flex justify-end mt-4">
            <Button label="Close" @click="closeCameraModal" class="p-button-secondary" />
          </div> -->
        </div>
      </div>
    </Transition>
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

import 'primeicons/primeicons.css'

const route = useRoute();
const clientName = ref(route.query.clientName || '');
const clientCode = ref(route.query.clientCode || '');
const coordinates = ref(null);
const capturedImage = ref(null);
const isCameraActive = ref(false);
const isCameraModalOpen = ref(false);
const address = ref(null); // To store the address
const toast = useToast();

const getLocation = () => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition((position) => {
      coordinates.value = {
        latitude: position.coords.latitude,
        longitude: position.coords.longitude,
      };
      // Call the function to get the address
      getAddressFromCoordinates(coordinates.value.latitude, coordinates.value.longitude);
    }, (error) => {
      console.error("Error getting location:", error);
      alert("Please allow location access.");
    });
  } else {
    alert("Geolocation is not supported by this browser.");
  }
};

// Function to get address from latitude and longitude
const getAddressFromCoordinates = async (lat, lon) => {
  try {
    const response = await fetch(`https://nominatim.openstreetmap.org/reverse?lat=${lat}&lon=${lon}&format=json`);
    const data = await response.json();
    if (data && data.display_name) {
      address.value = data.display_name; // Set the address
    } else {
      address.value = "Address not found.";
    }
  } catch (error) {
    console.error("Error fetching address:", error);
    address.value = "Error fetching address.";
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

    // Check if srcObject is supported
    if ('srcObject' in video) {
      video.srcObject = stream; // Use srcObject if available
    } else {
      // Fallback for older browsers
      video.src = URL.createObjectURL(stream);
    }

    isCameraActive.value = true;
  } catch (error) {
    console.error("Error accessing camera:", error);
    alert("Error accessing camera: " + error.message); // Show specific error message
  }
};

const captureImage = () => {
  const video = document.querySelector('video');
  const canvas = document.createElement('canvas');
  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;
  const context = canvas.getContext('2d');
  context.drawImage(video, 0, 0, canvas.width, canvas.height);
  capturedImage.value = canvas.toDataURL('image/png');
  closeCameraModal(); // Close the modal after capturing the image
};

const closeCameraModal = () => {
  isCameraModalOpen.value = false;
  isCameraActive.value = false;
  const video = document.querySelector('video');
  if (video.srcObject) {
    const stream = video.srcObject;
    const tracks = stream.getTracks();
    tracks.forEach(track => track.stop());
    video.srcObject = null;
  }
};

const validateStep1 = (activateCallback) => {
  if (coordinates.value) {
    activateCallback('2'); // Move to Step 2
  } else {
    alert("Please grant location access before proceeding.");
  }
};

const retakeCapture = () => {
  capturedImage.value = null; // Clear the captured image
  openCameraModal(); // Open the camera modal again
};

const validateStep2 = (activateCallback) => {
  activateCallback('3'); // Move to Step 3
};

const reloadPage = () => {
  location.reload(); // Reload the current page
};

const proceedToESign = () => {
  alert("Proceeding to E-Sign...");
};

onMounted(() => {
  // Any additional setup can go here
});
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}

/* Full-screen modal styling */
.fixed {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5); /* Semi-transparent black background */
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000; /* Ensure it's on top of other elements */
}

.bg-white {
  background-color: white; /* Ensure modal content has a white background */
}
</style>