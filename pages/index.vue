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
                <div class="border-gray-200 rounded bg-gray-50  font-medium">
                  <div class="card flex justify-center py-10">
                    <div class=" gap-2">
                      <div class="font-bold my-5">Hello, {{ clientName }} ({{ clientCode }})</div>
                      <div class="font-bold flex justify-center">
                        How to do
                      </div>
                      <div class="font-bold my-2"> <Button label="Enable GPS" @click="getLocation"  class="w-full"/></div>
                       <div class="font-bold my-2"> <Button label="Allow Camera" @click="startCamera" class="w-full"/></div>
                       
                      
                    </div>
                  </div>
                </div>
                <div class="flex p-4 justify-end bg-gray-50">
                  <Button label="Next" icon="pi pi-arrow-right" iconPos="right" @click="validateStep1(activateCallback)" />
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
                        <div class="font-bold my-2">{{ clientName }} ({{ clientCode }}),</div>
                        <div class="font-semibold">your Location is,</div>
                        <p> <span  class="font-semibold">Latitude: </span>
                            {{ coordinates.latitude }}</p>
                        <p> <span  class="font-semibold">Longitude:</span> {{ coordinates.longitude }}</p>
                      </div>
                      <div v-else class="mt-4 text-red-500">
                        <p>Please allow GPS access to get your location.</p>
                        <Button label="Reload" @click="reloadPage" />
                      </div>
                      <div v-if="isCameraActive && !capturedImage" class="mt-4">
                        <video ref="video" autoplay playsinline class="w-full h-[200px]"></video>
                        <div class="flex justify-center mt-2 px-5">
                          <Button label="Capture Image" class="w-full" @click="captureImage" />
                        </div>
                      </div>
                      <div v-if="capturedImage" class="mt-4 ">
                        <img :src="capturedImage" alt="Captured Image" class="w-full h-[200px] px-5"  />
                        <div class="flex justify-center mt-2 px-5">
                          <Button label="Retake" @click="retakeCapture" class="w-full"/>
                        </div>
                      </div>
                     
                    </div>
                  </div>
                </div>
                <div class="flex p-4 justify-between bg-gray-50">
                  <Button label="Back" severity="secondary" icon="pi pi-arrow-left" @click="activateCallback('1')" />
                  <Button label="Next" icon="pi pi-arrow-right" iconPos="right" @click="validateStep2(activateCallback)" />
                </div>
              </div>
            </div>
          </StepPanel>
          <StepPanel v-slot="{ activateCallback }" value="3">
          <div class="border-2 border-dashed rounded-lg shadow-lg">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 flex-auto flex justify-center items-center font-medium">
                <div class="card flex flex-col items-center justify-center py-10 px-6">
                  <h2 class="text-xl font-bold ">Thank You, {{ clientName }}!</h2>
                  <p class="text-lg text-gray-700 mt-2">Your IP verification is completed.</p>
                  <div class="mt-4">
                    <Button label="Proceed to E-Sign" @click="proceedToESign" class="bg-blue-600 text-white hover:bg-blue-700 transition duration-200" />
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
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import { useToast } from 'primevue/usetoast';
  import Toast from 'primevue/toast';
  import FileUpload from 'primevue/fileupload';
  import Button from 'primevue/button';
  import Textarea from 'primevue/textarea';
  import Stepper from 'primevue/stepper';
  import Step from 'primevue/step';
  import StepList from 'primevue/steplist';
  import StepPanel from 'primevue/steppanel';
  import Message from 'primevue/message';
  import { useRoute } from 'vue-router';
  
import 'primeicons/primeicons.css'




  // Accept clientName and clientCode as props
  const route = useRoute();
  const clientName = ref(route.query.clientName || ''); // Get clientName from route
  const clientCode = ref(route.query.clientCode || ''); // Get clientCode from route
  
  const address = ref(null);
  const files = ref([]);
  const fileErrorMessage = ref(null);
  const coordinates = ref(null);
  const isCameraActive = ref(false);
  const capturedImage = ref(null);
  const toast = useToast();
  
  const getLocation = () => {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition((position) => {
        coordinates.value = {
          latitude: position.coords.latitude,
          longitude: position.coords.longitude,
        };
      }, (error) => {
        console.error("Error getting location:", error);
        alert("Please allow location access.");
      });
    } else {
      alert("Geolocation is not supported by this browser.");
    }
  };
  
  const startCamera = async () => {
    try {
      isCameraActive.value = true;
      const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' } });
      const video = document.querySelector('video');
      video.srcObject = stream;
    } catch (error) {
      console.error("Error accessing camera:", error);
      alert("Error accessing camera.");
    }
  };
  
  const validateStep1 = (activateCallback) => {
    // Check if both location and camera permissions are granted
    if (coordinates.value && isCameraActive.value) {
      activateCallback('2'); // Move to Step 2
    } else {
      alert("Please grant both location and camera access before proceeding.");
    }
  };
  
  const validateStep2 = (activateCallback) => {
    // Add your validation logic for Step 2 here
    activateCallback('3'); // Move to Step 3
  };
  
  const captureImage = () => {
    const video = document.querySelector('video');
    const canvas = document.createElement('canvas');
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
    const context = canvas.getContext('2d');
    context.drawImage(video, 0, 0, canvas.width, canvas.height);
    capturedImage.value = canvas.toDataURL('image/png');
  };
  
  const retakeCapture = () => {
    capturedImage.value = null;
    isCameraActive.value = false;
    startCamera(); // Restart the camera
  };
  
  const reloadPage = () => {
    location.reload(); // Reload the current page
  };
  
  const proceedToESign = () => {
    // Logic to proceed to e-signing
    alert("Proceeding to E-Sign...");
    // You can navigate to another route or perform any action needed for e-signing
  };
  
  onMounted(() => {
 
  });
  </script>