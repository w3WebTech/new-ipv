<template>
  <div>
    <TransitionRoot as="template" :show="sidebarOpen">
      <Dialog class="relative z-50 lg:hidden" @close="sidebarOpen = false">
        <TransitionChild as="template" enter="transition-opacity ease-linear duration-300" enter-from="opacity-0"
          enter-to="opacity-100" leave="transition-opacity ease-linear duration-300" leave-from="opacity-100"
          leave-to="opacity-0">
          <div class="fixed inset-0 bg-gray-900/80" />
        </TransitionChild>

        <div class="fixed inset-0 flex">
          <TransitionChild as="template" enter="transition ease-in-out duration-300 transform"
            enter-from="-translate-x-full" enter-to="translate-x-0"
            leave="transition ease-in-out duration-300 transform" leave-from="translate-x-0"
            leave-to="-translate-x-full">
            <DialogPanel class="relative mr-16 flex w-full max-w-xs flex-1">
              <TransitionChild as="template" enter="ease-in-out duration-300" enter-from="opacity-0"
                enter-to="opacity-100" leave="ease-in-out duration-300" leave-from="opacity-100" leave-to="opacity-0">
                <div class="absolute left-full top-0 flex w-16 justify-center pt-5">
                  <button type="button" class="-m-2.5 p-2.5" @click="sidebarOpen = false">
                    <span class="sr-only">Close sidebar</span>
                    <XMarkIcon class="h-5 w-5 text-white" aria-hidden="true" />
                  </button>
                </div>
              </TransitionChild>
              <div class="flex grow flex-col gap-y-5 overflow-y-auto bg-indigo-600 px-6 pb-4">
                <div class="flex h-16 shrink-0 items-center">
                  <!-- <img class="h-5 w-20" src="https://tailwindui.com/plus-assets/img/logos/mark.svg?color=white"
                    alt="Your Company" /> -->
                    AP ADMIN
                </div>
                <nav class="flex flex-1 flex-col">
                  <ul role="list" class="flex flex-1 flex-col gap-y-7">
                    <li>
                      <ul role="list" class="-mx-2 space-y-1">
                        <li v-for="item in navigation" :key="item.name">
                          <a :href="item.href"
                            :class="[item.current ? 'bg-indigo-700 text-white' : 'text-indigo-200 hover:bg-indigo-700 hover:text-white', 'group flex gap-x-3 rounded-md p-2 text-sm/6 font-semibold']">
                            <component :is="item.icon"
                              :class="[item.current ? 'text-white' : 'text-indigo-200 group-hover:text-white', 'h-5 w-5 shrink-0']"
                              aria-hidden="true" />
                            {{ item.name }}
                          </a>
                        </li>
                      </ul>
                    </li>
                    <a href="#" @click.prevent="handleSignOut"
                      class="group -mx-2 flex gap-x-3 rounded-md p-2 text-sm/6 font-semibold text-indigo-200 hover:bg-indigo-700 hover:text-white">
                      <Cog6ToothIcon class="h-5 w-5 shrink-0 text-indigo-200 group-hover:text-white"
                        aria-hidden="true" />
                      Sign out
                    </a>

                  </ul>
                </nav>
              </div>
            </DialogPanel>
          </TransitionChild>
        </div>
      </Dialog>
    </TransitionRoot>

    <div class="hidden lg:fixed lg:inset-y-0 lg:z-50 lg:flex lg:w-72 lg:flex-col">
      <div class="flex grow flex-col gap-y-5 overflow-y-auto bg-indigo-600 px-6 pb-4">
        <div class=" items-center text-white font-bold pt-5  text-2xl">
          <!-- <img class="h-5 w-20" src="https://tailwindui.com/plus-assets/img/logos/mark.svg?color=white"
            alt="Your Company" /> -->
            AP ADMIN
        </div>

        <nav class="flex flex-1 flex-col">
          <ul role="list" class="flex flex-1 flex-col gap-y-7">
            <li>
              <ul role="list" class="-mx-2 space-y-1">
                <li v-for="item in navigation" :key="item.name">
                  <a :href="item.href"
                    :class="[item.current ? 'bg-indigo-700 text-white' : 'text-indigo-200 hover:bg-indigo-700 hover:text-white', 'group flex gap-x-3 rounded-md p-2 text-sm/6 font-semibold']">
                    <component :is="item.icon"
                      :class="[item.current ? 'text-white' : 'text-indigo-200 group-hover:text-white', 'h-5 w-5 shrink-0']"
                      aria-hidden="true" />
                    {{ item.name }}
                  </a>
                </li>
              </ul>
            </li>
            <li class="mt-auto">
              <a href="#"
                 >
                <!-- <Cog6ToothIcon class="h-5 w-5 shrink-0 text-indigo-200 group-hover:text-white" aria-hidden="true" click.prevent="handleSignOut" /> -->
                 <div @click.prevent="handleSignOut"  class="group -mx-2 flex gap-x-3 rounded-md p-2 text-sm/6 font-semibold text-indigo-200 hover:bg-indigo-700 hover:text-white ">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="h-5 w-5">
  <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 9V5.25A2.25 2.25 0 0 0 13.5 3h-6a2.25 2.25 0 0 0-2.25 2.25v13.5A2.25 2.25 0 0 0 7.5 21h6a2.25 2.25 0 0 0 2.25-2.25V15m3 0 3-3m0 0-3-3m3 3H9" />
</svg>
 LOGOUT
</div>
              </a>
            </li>
          </ul>
        </nav>
      </div>
    </div>

    <div class="lg:pl-72">
      <!-- <div
        class="sticky top-0 z-40 flex h-16 shrink-0 items-center gap-x-4 border-b border-gray-200 bg-white px-4 shadow-sm sm:gap-x-6 sm:px-6 lg:px-8">
        <button type="button" class="-m-2.5 p-2.5 text-gray-700 lg:hidden" @click="sidebarOpen = true">
          <span class="sr-only">Open sidebar</span>
          <Bars3Icon class="size-6" aria-hidden="true" />
        </button>

        <div class="h-6 w-px bg-gray-900/10 lg:hidden" aria-hidden="true" />

    <div class="flex flex-1 gap-x-4 self-stretch lg:gap-x-6">
          <form class="grid flex-1 grid-cols-1" action="#" method="GET">
            <input type="search" name="search" aria-label="Search"
              class="col-start-1 row-start-1 block size-full bg-white pl-8 text-base text-gray-900 outline-none placeholder:text-gray-400 sm:text-sm/6"
              placeholder="Search" />
            <MagnifyingGlassIcon class="pointer-events-none col-start-1 row-start-1 h-5 w-5 self-center text-gray-400"
              aria-hidden="true" />
          </form>
          <div class="flex items-center gap-x-4 lg:gap-x-6">
            <button type="button" class="-m-2.5 p-2.5 text-gray-400 hover:text-gray-500">
              <span class="sr-only">View notifications</span>
              <BellIcon class="h-5 w-5" aria-hidden="true" />
            </button>

            <div class="hidden lg:block lg:h-6 lg:w-px lg:bg-gray-900/10" aria-hidden="true" />

            <Menu as="div" class="relative">
              <MenuButton class="-m-1.5 flex items-center p-1.5">
                <span class="sr-only">Open user menu</span>
                <img class="h-10 w-10 rounded-full bg-gray-50"
                  src="https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80"
                  alt="" />
                <span class="hidden lg:flex lg:items-center">
                  <span class="ml-4 text-sm/6 font-semibold text-gray-900" aria-hidden="true">Tom Cook</span>
                  <ChevronDownIcon class="ml-2 h-5 w-5 text-gray-400" aria-hidden="true" />
                </span>
              </MenuButton>
              <transition enter-active-class="transition ease-out duration-100"
                enter-from-class="transform opacity-0 scale-95" enter-to-class="transform opacity-100 scale-100"
                leave-active-class="transition ease-in duration-75" leave-from-class="transform opacity-100 scale-100"
                leave-to-class="transform opacity-0 scale-95">
                <MenuItems
                  class="absolute right-0 z-10 mt-2.5 w-32 origin-top-right rounded-md bg-white py-2 shadow-lg ring-1 ring-gray-900/5 focus:outline-none">
                  <MenuItem v-for="item in userNavigation" :key="item.name" v-slot="{ active }">
                  <a v-if="item.name !== 'Sign out'" :href="item.href"
                    :class="[active ? 'bg-gray-50 outline-none' : '', 'block px-3 py-1 text-sm/6 text-gray-900']">
                    {{ item.name }}
                  </a>
                  <a v-else href="#" @click.prevent="handleSignOut"
                    :class="[active ? 'bg-gray-50 outline-none' : '', 'block px-3 py-1 text-sm/6 text-gray-900']">
                    {{ item.name }}
                  </a>
                  </MenuItem>
                </MenuItems>
              </transition>
            </Menu>
          </div>
        </div> 
      </div> -->

      <main class="py-3">
        <div class="px-4 sm:px-6 lg:px-8">
          <div>
            <div class="grid grid-cols-1 sm:hidden">
              <select aria-label="Select a tab"
                class="col-start-1 row-start-1 w-full appearance-none rounded-md bg-white py-2 pl-3 pr-8 text-base text-gray-900 outline outline-1 -outline-offset-1 outline-gray-300 focus:outline focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600"
                @change="handleTabChange">
                <option v-for="tab in tabs" :key="tab.name" :value="tab.href" :selected="tab.current">{{ tab.name }}
                </option>
              </select>
              <ChevronDownIcon
                class="pointer-events-none col-start-1 row-start-1 mr-2 size-5 self-center justify-self-end fill-gray-500"
                aria-hidden="true" />
            </div>
            <div class="hidden sm:block">
              <div class="border-b border-gray-200">
                <nav class="-mb-px flex space-x-8" aria-label="Tabs">
                  <a v-for="tab in tabs" :key="tab.name" :href="tab.href"
                    :class="[tab.current ? 'border-indigo-500 text-indigo-600' : 'border-transparent text-gray-500 hover:border-gray-300 hover:text-gray-700', 'whitespace-nowrap border-b-2 px-1 py-4 text-sm font-medium']"
                    :aria-current="tab.current ? 'page' : undefined" @click.prevent="setCurrentTab(tab)">
                    {{ tab.name }}
                  </a>
                </nav>
              </div>
            </div>

            <!-- Tab Content Section -->
            <div class="mt-4">
              <div v-if="tabs[0].current">
                <div v-if="openClients.length > 0">
                  <newtable :people="openClients" @view-client="openSidePanel" />
                </div>
                <div v-else>
                  <p>Loading...</p>
                </div>

              </div>
              <div v-if="tabs[1].current">
                <newtable :people="completedClients" @view-client="openSidePanel" />
                <!-- <Table :people="completedClients" @view-client="openSidePanel" /> -->
              </div>
              <div v-if="tabs[2].current">
                <newtable :people="approvedClients" @view-client="openSidePanel" />
                <!-- <Table :people="approvedClients" @view-client="openSidePanel" /> -->
              </div>
              <div v-if="tabs[3].current">
                <newtable :people="rejectedClients" @view-client="openSidePanel" />
                <!-- <Table :people="rejectedClients" @view-client="openSidePanel" /> -->
              </div>
            </div>

            <TransitionRoot as="template" :show="open">
              <Dialog class="relative z-10 " @close="open = false">
                <div class="fixed inset-0" />

                <div class="fixed inset-0 overflow-hidden">
                  <div class="absolute inset-0 overflow-hidden">
                    <div class="pointer-events-none fixed inset-y-0 right-0 flex max-w-full pl-10 sm:pl-16">
                      <TransitionChild as="template"
                        enter="transform transition ease-in-out duration-500 sm:duration-700"
                        enter-from="translate-x-full" enter-to="translate-x-0"
                        leave="transform transition ease-in-out duration-500 sm:duration-700" leave-from="translate-x-0"
                        leave-to="translate-x-full">
                        <DialogPanel class="pointer-events-auto w-screen max-w-2xl">
                          <div class="flex h-full flex-col overflow-y-scroll bg-white py-6 shadow-xl">
                            <div class="px-4 sm:px-6">
                              <!-- <div class="flex items-start justify-between">
                           
                                <div class="ml-3 flex h-7 items-center">
                                  <button type="button"
                                    class="relative rounded-md bg-white text-gray-400 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2"
                                    @click="open = false">
                                    <span class="absolute -inset-2.5" />
                                    <span class="sr-only">Close panel</span>
                                    <XMarkIcon class="size-6" aria-hidden="true" />
                                  </button>
                                </div>
                              </div> -->
                            </div>
                            <div class="relative mt-6 flex-1 px-2">
                              <!-- <Accortion :questions="selectedClient.questions" > -->
                             <!-- <questiontabs :questions="selectedClient.data" @approve="handleApprove"
                                @reject="handleReject" @updateStatus="handleUpdateStatus" />-->
                              <questiontabs 
  :questions="selectedClient.data" 
  :pdf-url="selectedClient.pdf"  
  @approve="handleApprove"
  @reject="handleReject"
  @updateStatus="handleUpdateStatus" 
/>


                            </div>
                          </div>
                        </DialogPanel>
                      </TransitionChild>
                    </div>
                  </div>
                </div>
              </Dialog>
            </TransitionRoot>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import Table from './components/Table.vue';
import newtable from './components/newtable.vue';
import {
  Dialog,
  DialogPanel,
  Menu,
  MenuButton,
  MenuItem,
  MenuItems,
  TransitionChild,
  TransitionRoot,
} from '@headlessui/vue';
import {
  Bars3Icon,
  BellIcon,
  Cog6ToothIcon,
  XMarkIcon,
  HomeIcon,
  UsersIcon,
  FolderIcon,
  CalendarIcon,
  DocumentDuplicateIcon,
  ChartPieIcon
} from '@heroicons/vue/24/outline';
import { ChevronDownIcon, MagnifyingGlassIcon } from '@heroicons/vue/20/solid';


const router = useRouter(); // Initialize the router
const handleSignOut = () => {
  console.log("Sign out clicked"); // Debugging
  localStorage.removeItem('email');
  localStorage.removeItem('password');
  router.push('/'); // Adjust the path to your login page
};

const navigation = [
  { name: 'Dashboard', href: '#', icon: HomeIcon, current: true },
];

const userNavigation = [
  { name: 'Your profile', href: '#' },
  { name: 'Sign out', href: '#' },
];



const openClients = ref([


]);
const completedClients = ref([]);
const approvedClients = ref([]);
const rejectedClients = ref([]);

const open = ref(false); // State to control the side panel visibility
const selectedClient = ref(null); // State to hold the currently selected client
const sidebarOpen = ref(false); // State to control the sidebar visibility

// Fetch the JSON data
// const fetchData = async () => {
//   try {
//     const response = await fetch('/inspectionadmin.json'); // Adjust the path if necessary
//     const data = await response.json();

//     // Assign the data to the respective reactive variables
//     openClients.value = data.clients.open;
//     completedClients.value = data.clients.completed;
//     approvedClients.value = data.clients.approved;
//     rejectedClients.value = data.clients.rejected;
//   } catch (error) {
//     console.error('Error fetching data:', error);
//   }
// };


// Call fetchData when the component is mounted
onMounted(() => {
  // Check local storage for required values
  const email = localStorage.getItem('email');
  const password = localStorage.getItem('password');

  if (!email || !password) {
    // Redirect to the index page if values are not available
    router.push('/'); // Adjust the path to your index page
  } else {
    fetchData('open'); // Fetch data if values are present
  }
});





const openSidePanel = (client) => {
  console.log("Selected Client: ", client); // Debugging
  getQuestions(client.RSessionId)

  // Open the side panel
};
const tabs = ref([
  { name: 'Open', href: '#open', current: true, status: 'open' },
  { name: 'Completed', href: '#completed', current: false, status: 'completed' },
  { name: 'Approved', href: '#approved', current: false, status: 'approved' },
  { name: 'Rejected', href: '#rejected', current: false, status: 'rejected' },
]);

const setCurrentTab = async (selectedTab) => {
  tabs.value.forEach(tab => {
    tab.current = tab.name === selectedTab.name;
  });
  window.location.hash = selectedTab.href;

  // Fetch data based on the selected tab's status
  await fetchData(selectedTab.status);
};
const handleUpdateStatus = async ({ status, RSessionId }) => {
  console.log("handleUpdateStatus");

  try {
    // Create a new FormData object
    const formData = new FormData();
    formData.append('RAppId', RSessionId);
    formData.append('status', status);
    formData.append('adminId', '243t5yt'); // Add the adminId

    // Call the appropriate API using FormData
    const response = await fetch('https://teamap.gwcindia.in/inspection/api/inspection-admin-action.php', {
      method: 'POST',
      body: formData // Send FormData as the body
    });

    const data = await response.json();

    // Optionally, you can refresh the data or update the local state here
    console.log(data);

    
    await fetchData('open'); 
    open.value=false;
  
  } catch (error) {
    console.error('Error updating status:', error);
  }
};

const fetchData = async (value) => {
  let status;
  let openData = [];
  let closeData = [];

  // Define status based on the provided value
  if (value === 'open') {
    status = '';
  } else if (value === 'completed') {
    status = 'close';
  } else if (value === 'approved') {
    status = '100';
  } else if (value === 'rejected') {
    status = '111';
  }

  try {
    // Fetch data from the API
    const response = await fetch(`https://teamap.gwcindia.in/inspection/api/inspection-read-api.php?status=${status}`);
    const data = await response.json();

    // Ensure data.data is valid and is an array
    if (Array.isArray(data.data)) {
      // Filter and categorize data based on 'row_count'
      if (value === 'open') {
        openData = data.data.filter(item => item.row_count < 33);
        closeData = data.data.filter(item => item.row_count >= 33);

        // Update reactive variables
        openClients.value = openData;
        completedClients.value = [...completedClients.value, ...closeData]; // Add to completed clients
      } else if (value === 'completed') {
        completedClients.value = data.data;
      } else if (value === 'approved') {
        approvedClients.value = data.data;
      } else if (value === 'rejected') {
        rejectedClients.value = data.data;
      }

      console.log(`${status} Clients:`, data.data);
    } else {
      console.error('Data format is invalid:', data);
    }
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};

const handleTabChange = (event) => {
  const selectedHref = event.target.value;
  const selectedTab = tabs.value.find(tab => tab.href === selectedHref);
  if (selectedTab) {
    setCurrentTab(selectedTab);
  }
};

const getQuestions = async (sessionId) => {
  try {
    const response = await fetch(`https://teamap.gwcindia.in/inspection/api/inspection-read-api.php?RAppId=${sessionId}`);

    const data = await response.json();
    selectedClient.value = data;
    console.log(selectedClient.value, "selectedClient.value")
    open.value = true;


  } catch (error) {
    console.error('Error fetching data:', error);
  }
};
const handleApprove = async (questionId) => {
  console.log("handleApprove");
  try {
    // Call an API to update the status to approved
    const response = await fetch(`https://teamap.gwcindia.in/inspection/api/inspection-read-api.php?=${questionId}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ questionId, status: 'approved' }) // pass the questionId to approve
    });
    const data = await response.json();
    // Update local state to reflect the approved status
    updateClientStatus(questionId, 'approved');
  } catch (error) {
    console.error('Error approving question:', error);
  }
};

const handleReject = async (questionId) => {
  console.log("handleApprove");
  try {
    // Call an API to update the status to approved
    const response = await fetch(`https://teamap.gwcindia.in/inspection/api/inspection-read-api.php?=${questionId}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ questionId, status: 'approved' }) // pass the questionId to approve
    });
    const data = await response.json();
    // Update local state to reflect the approved status
    updateClientStatus(questionId, 'approved');
  } catch (error) {
    console.error('Error approving question:', error);
  }
};

const updateClientStatus = (questionId, status) => {
  const question = selectedClient.value.data.find(q => q.id === questionId);
  if (question) {
    question.status = status; // Update the status locally
  }
};

</script>

<style scoped>
/* Add any additional styles here */
</style>
