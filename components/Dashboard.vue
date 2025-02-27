<template>
  <div class="card flex justify-center">
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
              <div class="border-gray-200 rounded bg-gray-50 flex-auto flex justify-center items-center font-medium">
                <div class="card flex justify-center py-10">
                  <div class="flex flex-col gap-2">
                    <!-- <label for="username">Username</label>
                    <InputText
                      id="username"
                      v-model="Username"
                      aria-describedby="username-help"
                      :class="{'p-invalid': usernameErrorMessage}" 
                    />
                    <Message v-if="usernameErrorMessage" severity="error" class="mt-2 text-xs">
                      {{ usernameErrorMessage }}
                    </Message> -->
                       <!-- Location info -->
            <div v-if="coordinates" class="mt-4">
              <p>Latitude: {{ coordinates.latitude }}</p>
              <p>Longitude: {{ coordinates.longitude }}</p>
            </div>

            <!-- Live Camera (Show only if camera is active and no image captured) -->
            <div v-if="isCameraActive && !capturedImage" class="mt-4">
              <video ref="video" autoplay playsinline class="w-full h-[200px]"></video>

              <div class="flex justify-center mt-2">
                <Button label="Capture Image" @click="captureImage" />
              </div>
            </div>

            <!-- Captured Image and Retake Button (Show only if image is captured) -->
            <div v-if="capturedImage" class="mt-4">
              <img :src="capturedImage" alt="Captured Image" class="w-full h-[200px]" />
              <div class="flex justify-center mt-2">
                <Button label="Retake" @click="retakeCapture" />
              </div>
            </div>

            <!-- Access Camera Button (Show only if camera is not active and no image captured) -->
            <div v-if="!isCameraActive && !capturedImage" class="mt-4 flex justify-center">
              <Button label="Access Camera" @click="startCamera" />
            </div>
                  </div>
                </div>
              </div>
            </div>

         

            <div class="flex p-4 justify-end bg-gray-50">
              <Button label="Next" icon="pi pi-arrow-right" iconPos="right" @click="validateStep1(activateCallback)" />
            </div>
          </div>
        </StepPanel>

        <!-- Step 2 Panel -->
        <StepPanel v-slot="{ activateCallback }" value="2">
          <div class="border-2 border-dashed">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 flex-auto flex justify-center items-center font-medium">
                <div class="card flex gap-6 items-center justify-center py-10">
                  <Toast />
                  <div>
                    <FileUpload
                      ref="fileUploadRef"
                      mode="basic"
                      name="demo[]"
                      url="/api/upload"
                      accept=".png,.jpg,.jpeg,.pdf"
                      :maxFileSize="5250000"
                      @upload="onUpload"
                      @select="onSelectedFiles"
                      :files="files"
                    />
                    <Message v-if="fileErrorMessage" severity="danger" class="my-5 px-5 text-red-500 text-xs">
                      {{ fileErrorMessage }}
                    </Message>
                  </div>
                  <div v-if="files.length > 0" class="flex gap-2 mt-4">
                    <Button icon="pi pi-eye" severity="info" class="rounded" @click="showPreview" />
                    <Button icon="pi pi-times" @click="removeFile" severity="danger" />
                  </div>
                </div>
              </div>
            </div>
            <div class="flex p-4 justify-between bg-gray-50">
              <Button label="Back" severity="secondary" icon="pi pi-arrow-left" @click="activateCallback('1')" />
              <Button label="Next" icon="pi pi-arrow-right" iconPos="right" @click="validateStep2(activateCallback)" />
            </div>
          </div>
        </StepPanel>

        <!-- Step 3 Panel -->
        <StepPanel v-slot="{ activateCallback }" value="3">
          <div class="border-2 border -dashed">
            <div class="flex flex-col h-full">
              <div class="border-gray-200 rounded bg-gray-50 flex-auto flex justify-center items-center font-medium">
                <div class="card flex justify-center py-10">
                  <div class="flex flex-col gap-2">
                    <label for="address">ADDRESS</label>
                    <Textarea v-model="address" rows="5" cols="30" />
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
import InputText from 'primevue/inputtext';
import Textarea from 'primevue/textarea';
import Stepper from 'primevue/stepper';
import Step from 'primevue/step';
import StepList from 'primevue/steplist';
import StepPanel from 'primevue/steppanel';
import Message from 'primevue/message';

// Accept clientName and clientCode as props
const { clientName, clientCode } = defineProps({
  clientName: String,
  clientCode: String
});

const Username = ref(null);
const address = ref(null);
const files = ref([]);
const previewVisible = ref(false);
const fileErrorMessage = ref(null);
const usernameErrorMessage = ref(null);
const coordinates = ref(null);
const fileUploadRef = ref(null);
const toast = useToast();
const isCameraActive = ref(false);
const capturedImage = ref(null);

const validateStep1 = (activateCallback) => {
  debugger
  postapi()
  if (!Username.value) {
    usernameErrorMessage.value = 'Username is required.';
  } else {
    usernameErrorMessage.value = null;
    activateCallback('2');
  }
};

const validateStep2 = (activateCallback) => {
  if (files.value.length === 0) {
    fileErrorMessage.value = 'Please upload at least one file.';
  } else {
    fileErrorMessage.value = null;
    activateCallback('3');
  }
};

const onSelectedFiles = (event) => {
  if (files.value) {
    fileErrorMessage.value = '';
  }
  files.value = event.files.map((file) => {
    return {
      ...file,
      name: file.name,
      type: file.type,
      objectURL: URL.createObjectURL(file),
    };
  });
};

const removeFile = () => {
  files.value = [];
  if (fileUploadRef.value) {
    fileUploadRef.value.clear();
  }
  toast.add({ severity: 'info', summary: 'Success', detail: 'Files removed successfully', life: 3000 });
};

const showPreview = () => {
  previewVisible.value = true;
};

const closePreview = () => {
  previewVisible.value = false;
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

const stopCamera = () => {
  const video = document.querySelector('video');
  const stream = video.srcObject;
  const tracks = stream?.getTracks();
  tracks?.forEach(track => track.stop());
};

const captureImage = () => {
  const video = document.querySelector('video');
  const canvas = document.createElement('canvas');
  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;
  const context = canvas.getContext('2d');
  
  // Draw the current video frame onto the canvas
  context.drawImage(video, 0, 0, canvas.width, canvas.height);

  // Convert the canvas content to a base64 image
  capturedImage.value = canvas.toDataURL('image/png');
};

const retakeCapture = () => {
  capturedImage.value = null;
  isCameraActive.value = false;
  startCamera(); // Restart the camera
};

const getLocation = () => {
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition((position) => {
      coordinates.value = {
        latitude: position.coords.latitude,
        longitude: position.coords.longitude,
      };
    });
  }
};

onMounted(() => {

  getLocation();
});
const postapi = async () => {

debugger



  // Prepare the data to be sent
  const postData = {
    lat: coordinates.value?.latitude || null, // Latitude
    lon: coordinates.value?.longitude || null, // Longitude
    name: "name", // Name
    clientCode: "ClientCode",
    image: capturedImage.value || null, // Image (base64 string)
    date: new Date().toISOString().split('T')[0], // Current date in YYYY-MM-DD format
    time: new Date().toLocaleTimeString(), // Current time
  };

  try {
    const response = await fetch('http://192.168.0.106/new/client.php', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(postData), // Send the prepared data
    });
    
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    
    const data = await response.json();
    console.log(data);  // Optional: log the response to the console
  } catch (error) {
    console.error('Error during fetch:', error);
  }
};
</script>

