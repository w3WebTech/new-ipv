<template>
    <div class="card text-sm font-smeibold flex justify-between ">
        <div>
      <div class="">
        <p>{{ client.companyName }}</p>
        <p><span class="text-blue-500">ID:</span class="text-blue-500"> {{ client.clientId }}</p>
      </div>
      <div class="card-details">
        <p><span class="text-blue-500">Employee ID:</span class="text-blue-500"> {{ client.employeeId }}</p>
        <p><span class="text-blue-500">Employee Code:</span class="text-blue-500"> {{ client.empCode }}</p>
        <p><span class="text-blue-500">Date:</span class="text-blue-500"> {{ client.date }}</p>
        <p><span class="text-blue-500">Time:</span class="text-blue-500"> {{ client.time }}</p>
        <p><span class="text-blue-500">Question ID:</span class="text-blue-500"> {{ client.questionId }}</p>
      </div>
    </div>
      <div class="card-action">
        <div class="view-icon  rounded-md p-2" @click="viewClient(client)"><svg xmlns="http://www.w3.org/2000/svg" fill="white" viewBox="0 0 24 24" stroke-width="1.5" stroke="black" class="h-6 w-6">
  <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 0 1 0-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178Z" />
  <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
</svg>
</div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { defineProps, defineEmits } from 'vue';
  
  const props = defineProps({
    client: {
      type: Object,
      required: true,
    },
  });
  
  const emit = defineEmits();
  
  const viewClient = (client) => {
    emit('view-client', client); // Emit an event to open the side panel and pass the client data
  };
  </script>
  
  <style scoped>
  .card {
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    padding: 16px;
    margin: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
 
  
  .card-details {
    margin-top: 10px;
  }
  
  .card-action {
    text-align: right;
    margin-top: 20px;
  }
  </style>