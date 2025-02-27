<template>
  <div class="px-4 sm:px-6 lg:px-8">
    <div class="mt-8 flow-root">
      <div class="-mx-4 -my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
        <div class="inline-block min-w-full py-2 align-middle sm:px-6 lg:px-8">
          <div class="overflow-hidden shadow ring-1 ring-black/5 sm:rounded-lg">
            <div class="flex justify-between items-center p-4">
              <input type="text" v-model="searchTerm" placeholder="Search..."
                class="border rounded p-2 focus:outline-none focus:ring-0" />
              <!-- <button @click="downloadPDF" class="bg-blue-500 text-white px-4 py-2 rounded">
                Download PDF
              </button> -->
            </div>
            <table class="min-w-full divide-y divide-gray-300">
              <thead class="bg-gray-50">
                <tr>
                  <th @click="sort('id')" scope="col"
                    class="cursor-pointer py-3.5 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-6 ">
                    <a href="#" class="group inline-flex">
                      Id
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a>
                  </th>
                  <th @click="sort('customerId')" scope="col"
                    class="cursor-pointer px-3 text-left text-sm font-semibold text-gray-900 ">

                    <a href="#" class="group inline-flex">
                      Customer Id
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a>

                  </th>
                  <th @click="sort('companyName')" scope="col"
                    class="cursor-pointer px-3 py-3.5 text-left text-sm font-semibold text-gray-900"> <a href="#"
                      class="group inline-flex">
                      Company Name
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a></th>
                  <th @click="sort('EmpId')" scope="col"
                    class="cursor-pointer px-3 py-3.5 text-left text-sm font-semibold text-gray-900"> <a href="#"
                      class="group inline-flex">
                      Employee Id
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a></th>
                  <th @click="sort('EmpName')" scope="col"
                    class="cursor-pointer py-3.5 pl-4 pr-3 text-left text-sm font-semibold text-gray-900 sm:pl-6"> <a
                      href="#" class="group inline-flex">
                      Emp Name
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a></th>
                  <th @click="sort('date')" scope="col"
                    class="cursor-pointer px-3 py-3.5 text-left text-sm font-semibold text-gray-900"> <a href="#"
                      class="group inline-flex">
                      Date
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a></th>
                  <th @click="sort('time')" scope="col"
                    class="cursor-pointer px-3 py-3.5 text-left text-sm font-semibold text-gray-900"> <a href="#"
                      class="group inline-flex">
                      Time
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a></th>
                  <th @click="sort('questionId')" scope="col"
                    class="cursor-pointer px-3 py-3.5 text-left text-sm font-semibold text-gray-900"> <a href="#"
                      class="group inline-flex">
                      Question Id
                      <span class="ml-2 flex-none rounded bg-gray-100 text-gray-900 group-hover:bg-gray-200">
                        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                          stroke="currentColor" class="h-4 w-4 mt-1 mx-1 ">
                          <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3 7.5 7.5 3m0 0L12 7.5M7.5 3v13.5m13.5 0L16.5 21m0 0L12 16.5m4.5 4.5V7.5" />
                        </svg>
                      </span>
                    </a></th>
                  <th scope="col" class="relative py-3.5 pl-3 pr-4 sm:pr-6 ">
                    <span class="sr-only">View</span>
                  </th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200 bg-white">
                <tr v-for="person in filteredPeople" :key="person.id">
                  <td class="whitespace-nowrap py-4 pl-4 pr-3 text-sm font-medium text-gray-900 sm:pl-6 ">{{ person.id }}
                  </td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">{{ person.customerId }}</td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">{{ person.companyName }}</td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">{{ person.EmpId }}</td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">{{ person.EmpName }}</td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">{{ person.date }}</td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500">{{ person.time }}</td>
                  <td class="whitespace-nowrap px-3 py-4 text-sm text-gray-500 flex ">{{ person.questionId }}</td>
                  <td class="relative whitespace-nowrap py-4 pl-3 pr-4 text-right text-sm font-medium sm:pr-6">
                    <a href="#" class="text-indigo-600 hover:text-indigo-900" @click.prevent="viewClient(person)">
                      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="indigo" class="h-5 w-5">
                        <path stroke-linecap="round" stroke-linejoin="round"
                          d="M2.036 12.322a1.012 1.012 0 0 1 0-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178Z" />
                        <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
                      </svg>
                      <span class="sr-only">View details</span>
                    </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import { defineProps, defineEmits, ref, computed } from 'vue';
import jsPDF from 'jspdf';
import 'jspdf-autotable';

const props = defineProps({
  people: {
    type: Array,
    required: true,
  },
});

const emit = defineEmits();

const searchTerm = ref('');
const sortKey = ref('id');
const sortOrder = ref('asc');

const filteredPeople = computed(() => {
  let filtered = props.people.filter(person => {
    return Object.values(person).some(value =>
      String(value).toLowerCase().includes(searchTerm.value.toLowerCase())
    );
  });

  // Sort the filtered results
  filtered.sort((a, b) => {
    const modifier = sortOrder.value === 'asc' ? 1 : -1;
    if (a[sortKey.value] < b[sortKey.value]) return -1 * modifier;
    if (a[sortKey.value] > b[sortKey.value]) return 1 * modifier;
    return 0;
  });

  return filtered;
});

const sort = (key) => {
  if (sortKey.value === key) {
    sortOrder.value = sortOrder.value === 'asc' ? 'desc' : 'asc';
  } else {
    sortKey.value = key;
    sortOrder.value = 'asc';
  }
};

const viewClient = (client) => {
  emit('view-client', client); // Emit an event to open the side panel and pass the client data
};

const downloadPDF = () => {
  const doc = new jsPDF();
  doc.autoTable({
    head: [['ID', 'Customer Id', 'Company Name', 'Employee Id', 'Emp Name', 'Date', 'Time', 'Question Id']],
    body: filteredPeople.value.map(person => [
      person.id,
      person.customerId,
      person.companyName,
      person.EmpId,
      person.EmpName,
      person.date,
      person.time,
      person.questionId,
    ]),
  });
  doc.save('table.pdf');
};
</script>