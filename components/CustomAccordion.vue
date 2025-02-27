<template>
    <div class="card">
        <div v-for="(question, index) in questions" :key="index" class="border-b">
            <div @click="toggle(index)" class="cursor-pointer flex justify-between items-center p-4">
                <h3 class="font-medium">{{ question.question }}</h3>
                <span>{{ isActive(index) ? '-' : '+' }}</span>
            </div>
            <div v-if="isActive(index)" class="p-4">
                <p class="m-0">{{ question.message || 'No answer provided' }}</p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
    questions: {
        type: Array,
        required: true,
    },
});

const activeIndex = ref(null);

const toggle = (index) => {
    activeIndex.value = activeIndex.value === index ? null : index;
};

const isActive = (index) => {
    return activeIndex.value === index;
};
</script>

<style scoped>
.card {
    border: 1px solid #e5e7eb; /* Tailwind gray-300 */
    border-radius: 0.375rem; /* Tailwind rounded-md */
    margin-bottom: 1rem; /* Tailwind mb-4 */
}
</style>
