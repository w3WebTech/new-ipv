<template>
    <span>
      <svg v-if="isActive" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
        stroke="currentColor" class="h-4 w-4 mt-1 mx-1">
        <path stroke-linecap="round" stroke-linejoin="round" d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
      </svg>
    </span>
  </template>
  
  <script setup>
  defineProps({
    isActive: {
      type: Boolean,
      required: true,
    },
    isAsc: {
      type: Boolean,
      required: true,
    },
  });
  </script>
  
  <style scoped>
  /* Add any additional styles for the SortIcon here */
  .size-6 {
    width: 1.5rem; /* Adjust size as needed */
    height: 1.5rem; /* Adjust size as needed */
  }
  </style>