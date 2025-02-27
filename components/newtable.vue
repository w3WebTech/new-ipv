<template>
    <div class="px-4 sm:px-4 lg:px-4">
        <div class="mt-8 flow-root">
            <div class="-mx-4 -my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
                <div class="inline-block min-w-full py-2 align-middle sm:px-6 lg:px-8">
                    <div class="overflow-hidden shadow ring-1 ring-black/5 sm:rounded-lg">
                        <div class="flex justify-between items-center p-4">
                            <input
                                type="text"
                                v-model="searchTerm"
                                placeholder="Search..."
                                class="border rounded p-2 focus:outline-none focus:ring-0"
                            />
                        </div>
                        <DataTable :value="filteredClients" showGridlines class="rounded-md text-sm" paginator :rows="10" :rowsPerPageOptions="[10,20,40,50]" >
                            <Column field="EmpId" header="Employee ID" sortable></Column>
                            <Column field="EmpName" header="Employee Name" sortable></Column>
                            <Column field="CustomerId" header="Customer ID" sortable></Column>
                            <Column field="CompanyName" header="Company Name" sortable></Column>
                            <Column field="State" header="State" sortable></Column>
                            <Column field="RecordDate" header="Date" sortable></Column>
                            <Column field="RecordTime" header="Time" sortable></Column>
                            <!-- <Column field="status" header="Status" sortable></Column> -->
                            <Column field="RSessionId" header="RSessionId" sortable></Column>
                            <Column header="Action">
                                <template #body="slotProps">
                                    <Button
                                        icon="pi pi-eye"
                                        rounded
                                        aria-label="View"
                                        @click.prevent="viewClient(slotProps.data)"
                                        severity="secondary"
                                    />
                                </template>
                            </Column>
                            <template #paginatorend>
    <span class="">Total Records :{{ filteredClients.length }}</span>
</template>

                            <template #empty ><span class="flex justify-center  item-center text-center"></span> No Records Found ! </template>
                        </DataTable>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, defineProps, defineEmits } from 'vue';

import 'primeicons/primeicons.css'

const props = defineProps({
    people: {
        type: Array,
        default: () => [] // Default to an empty array
    },
});
console.log(props.people, "people"); // Log the people prop

const emit = defineEmits(['view-client']);

const searchTerm = ref('');

// Computed property for filtered clients based on search term
const filteredClients = computed(() => {
    return (props.people || [])
        .filter(client => 
            Object.values(client).some(value =>
                String(value).toLowerCase().includes(searchTerm.value.toLowerCase())
            )
        )
        .sort((a, b) => new Date(b.RecordDate) - new Date(a.RecordDate)); // Sorting in descending order
});
// Function to handle viewing a specific client
const viewClient = (client) => {

    console.log("Viewing client:", client);
    emit('view-client', client); // Emit an event to open the side panel and pass the client data
};
</script>

<style scoped>
/* Add any additional styles here */
</style>
