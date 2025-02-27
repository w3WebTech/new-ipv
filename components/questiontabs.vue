<template>
  <div class="mt-1 px-5">
    <h4 class="text-gray-500 font-semibold">Questions</h4>
    <div class="text-sm text-gray-500 font-semibold my-4">
      <div class="flex gap-4">
        <div>
          <div class="flex justify-start space-x-2 mb-2">
            <span class="text-gray-400 font-medium">CustomerId:</span>
            <span>{{ questions[0].CustomerId }}</span>
          </div>
          <div class="flex justify-start space-x-2 mb-2">
            <span class="text-gray-400 font-medium">EmpId:</span>
            <span>{{ questions[0].EmpId }}</span>
          </div>
        </div>
        <div>
          <div class="flex justify-start space-x-2 mb-2">
            <span class="text-gray-400 font-medium">CompanyName:</span>
            <span>{{ questions[0].CompanyName }}</span>
          </div>
          <div class="flex justify-start space-x-2 mb-2">
            <span class="text-gray-400 font-medium">EmpName:</span>
            <span>{{ questions[0].EmpName }}</span>
          </div>
        </div>

        <div class="flex justify-start space-x-2 mb-2">
          <span class="text-gray-400 font-medium">State:</span>
          <span>{{ questions[0].State }}</span>
        </div>
      </div>



    </div>


    <div class="grid grid-cols-1 sm:hidden ">
      <select aria-label="Select a tab"
        class="col-start-1 row-start-1 w-full appearance-none rounded-md bg-white py-2 pl-3 pr-8 text-base text-gray-900 outline outline-1 -outline-offset-1 outline-gray-300 focus:outline focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600"
        @change="handleTabChange">
        <option v-for="tab in tabs" :key="tab.name" :value="tab.name" :selected="tab.current">
          {{ tab.name }}
        </option>
      </select>
      <ChevronDownIcon
        class="pointer-events-none col-start-1 row-start-1 mr-2 size-5 self-center justify-self-end fill-gray-500"
        aria-hidden="true" />
    </div>

    <!-- Desktop Tabs Navigation -->
    <div class="hidden sm:block">
      <div class="border-b border-gray-200">
        <nav class="-mb-px flex space-x-8" aria-label="Tabs">
          <a v-for="tab in tabs" :key="tab.name" :href="tab.href" :class="[
            tab.current ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:border-gray-300 hover:text-gray-700',
            'whitespace-nowrap border-b-2 px-1 py-4 text-sm font-medium'
          ]" :aria-current="tab.current ? 'page' : undefined" @click.prevent="setCurrentTab(tab)">
            {{ tab.name }}
          </a>
        </nav>
      </div>
    </div>

    <!-- Tab Content Section -->
    <div class="mt-4 mb-40">
      <div v-if="tabs[0].current">
        <Accordion :value="null">
          <AccordionPanel v-for="(question, index) in chunkedQuestions[0]" :key="index" :value="index">
            <AccordionHeader>{{ question.Question }}</AccordionHeader>
            <AccordionContent>

              <div class="text-sm text-gray-500 font-semibold">
                <div class="flex justify-start space-x-4 mb-2">

                  <span class="text-gray-400 font-medium">Answer:</span>
                  <span>{{ question.Message ? question.Message : "Message Not Available" }}</span>
                </div>

                <div v-if="question.ImageUrl" class="flex items-center space-x-4">
                  <img :src="'https://teamap.gwcindia.in/inspection/api/images/' + question.ImageUrl" alt="Answer Image"
                    class="w-24 h-24 object-cover" />
                </div>
              </div>


            </AccordionContent>
          </AccordionPanel>
        </Accordion>
      </div>
      <div v-if="tabs[1].current">
        <Accordion :value="null">
          <AccordionPanel v-for="(question, index) in chunkedQuestions[1]" :key="index" :value="index">
            <AccordionHeader class="truncate">{{ question.Question }}</AccordionHeader>
            <AccordionContent class="text-sm">

              <div class="text-sm text-gray-500 font-semibold">
                <div class="flex justify-start space-x-4 mb-2">

                  <span class="text-gray-400 font-medium">Answer:</span>
                  <span>{{ question.Message ? question.Message : "Message Not Available" }}</span>
                </div>

                <div v-if="question.ImageUrl" class="flex items-center space-x-4">
                  <img :src="'https://teamap.gwcindia.in/inspection/api/images/' + question.ImageUrl" alt="Answer Image"
                    class="w-24 h-24 object-cover" />
                </div>
              </div>

            </AccordionContent>
          </AccordionPanel>
        </Accordion>
      </div>
      <div v-if="tabs[2].current">
        <Accordion :value="null">
          <AccordionPanel v-for="(question, index) in chunkedQuestions[2]" :key="index" :value="index">
            <AccordionHeader>{{ question.Question }}</AccordionHeader>
            <AccordionContent class="text-sm">
              <div class="text-sm text-gray-500 font-semibold">
                <div class="flex justify-start space-x-4 mb-2">

                  <span class="text-gray-400 font-medium">Answer:</span>
                  <span>{{ question.Message ? question.Message : "Message Not Available" }}</span>
                </div>

                <div v-if="question.ImageUrl" class="flex items-center space-x-4">
                  <img :src="'https://teamap.gwcindia.in/inspection/api/images/' + question.ImageUrl" alt="Answer Image"
                    class="w-24 h-24 object-cover" />
                </div>
              </div>

            </AccordionContent>
          </AccordionPanel>
        </Accordion>
      </div>
    </div>
    <div class="fixed bottom-0 w-full bg-white p-4"
      v-if="questions[0]?.status !== '111' && questions[0]?.status !== '100' && pdfUrl !== ''">
      <div class="flex justify-spacearound gap-4 px-20">
        <Button label="Approve" severity="secondary" variant="outlined"
          @click="handleApprove(questions[0].RSessionId)" />
        <Button label="Reject" severity="secondary" variant="outlined" @click="handleReject(questions[0].RSessionId)" />
        <Button label="Download Pdf" severity="secondary" variant="outlined" @click="downloadPDF(pdfUrl)" />

      </div>
    </div>

    <div class="fixed bottom-0 w-full bg-white p-4"
      v-if="questions[0]?.status !== '111' && questions[0]?.status !== '100' && pdfUrl === ''">
      <div class="flex justify-spacearound gap-4 px-40">
        <Button label="Approve" severity="secondary" variant="outlined"
          @click="handleApprove(questions[0].RSessionId)" />
        <Button label="Reject" severity="secondary" variant="outlined" @click="handleReject(questions[0].RSessionId)" />
      </div>
    </div>


  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { ChevronDownIcon } from '@heroicons/vue/16/solid';
import { defineProps, defineEmits } from 'vue';
import Accordion from 'primevue/accordion';
import AccordionPanel from 'primevue/accordionpanel';
import AccordionHeader from 'primevue/accordionheader';
import AccordionContent from 'primevue/accordioncontent';

// Props for the questions data
// Modify props to accept pdfUrl
const props = defineProps({
  questions: Array,
  pdfUrl: String,  // Accept the PDF URL from the parent
});

console.log(props.questions, "prpdata");
const emit = defineEmits(['updateStatus']); // Define an event called 'updateStatus'

const tabs = ref([
  { name: '1 to 11 questions', href: '#1to11', current: true },
  { name: '12 to 22 questions', href: '#12to22', current: false },
  { name: '23 to 33 questions', href: '#23to33', current: false },
]);

const downloadPDF = (pdfUrl) => {
  if (!pdfUrl) {
    console.error('No PDF URL provided.');
    return;
  }

  // Create an invisible anchor tag to trigger the download
  const link = document.createElement('a');
  link.href = pdfUrl; // Use the pdfUrl passed as prop

  // Extract the file name (optional)
  const fileName = pdfUrl.split('/').pop(); // Extract the filename from the URL
  link.download = fileName;

  // Trigger the download
  link.click();
};



const chunkedQuestions = computed(() => {
  const chunkSize = 11;
  const chunks = [];
  for (let i = 0; i < props.questions.length; i += chunkSize) {
    chunks.push(props.questions.slice(i, i + chunkSize));
  }
  console.log(chunks); // Add this line to see if the chunks are populated
  return chunks;
});


// Set the current tab
const setCurrentTab = (selectedTab) => {
  tabs.value.forEach(tab => {
    tab.current = tab.name === selectedTab.name;
  });
  window.location.hash = selectedTab.href;
};

// Handle the tab change from the dropdown (mobile)
const handleTabChange = (event) => {
  const selectedTabName = event.target.value;
  const selectedTab = tabs.value.find(tab => tab.name === selectedTabName);
  if (selectedTab) {
    setCurrentTab(selectedTab);
  }
};
const handleApprove = (value) => {
  emit('updateStatus', { status: "100", RSessionId: value });
};

const handleReject = (value) => {
  emit('updateStatus', { status: '111', RSessionId: value });
};
</script>

<style scoped>
/* Add any custom styles here */
</style>
