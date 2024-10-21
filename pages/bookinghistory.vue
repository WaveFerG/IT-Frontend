<template>
  <Navbar />

  <div class="flex bg-gray-200">
    <div class="fixed flex flex-col mr-10 max-w-xs">
      <v-sheet elevation="10" class="bg-white" style="max-width: 280px; height: 400px; border-bottom: 1px solid #D3D3D3;">
        <v-btn class="mt-7 ml-5" href="/home">
          <v-icon class="mb-2" color="#757575" size="30">mdi-home</v-icon>
          <span class="text-h6 ml-2 text-gray-500">Home</span>
        </v-btn>
        <v-btn class="mt-7 ml-5" href="/bookinghistory">
          <v-icon class="mb-2" size="30" color="#757575">mdi-home</v-icon>
          <span class="text-h6 ml-2 text-gray-500">ประวัติการจอง</span>
        </v-btn>
      </v-sheet>

      <v-sheet elevation="10" class="bg-white flex-auto" style="max-width: 280px; height: 50vh;">
        <div class="p-2 ml-5 text-gray-500">
          Soradech.ksb@gmail.com
        </div>
        <div class="p-2 ml-5 text-gray-500">
          0928071423
        </div>
        <div class="p-2 ml-5 text-gray-500">
          Line: wavefergamer
        </div>
      </v-sheet>
    </div>

    <div class="flex flex-col ml-80 " style="width: 1000px;">
      <div class="flex">
        <v-card-text class="mt-5 ml-4">
          <v-text-field
            v-model="searchQuery"
            append-inner-icon="mdi-magnify"
            density="compact"
            label="ค้นหาห้อง"
            variant="solo"
            hide-details
            single-line
          />
        </v-card-text>
      </div>

      <v-card class="mt-5" style="width: 1100px;">
        <v-card-title class="border-b-md mt-1 ml">
          <a class="font-semibold">ประวัติข้อมูลการจองห้อง</a>
        </v-card-title>
        <v-row class="ml-5">
          <v-col md="1" class="font-semibold border-b-md mt-1">ID</v-col>
          <v-col md="2" class="font-semibold border-b-md mt-1">หัวข้อประชุม</v-col>
          <v-col md="2" class="font-semibold border-b-md mt-1">วันที่จองไว้</v-col>
          <v-col md="2" class="font-semibold border-b-md mt-1">วันและเวลาที่เริ่ม</v-col>
          <v-col md="2" class="font-semibold border-b-md mt-1">วันและเวลาที่สิ้นสุด</v-col>
          <v-col md="2" class="font-semibold border-b-md mt-1">Status</v-col>
        </v-row>
        <v-card-text>
          <v-row v-for="room in paginatedRooms" :key="room.booking_id" class="flex border-b my-3 ml-3">
            <v-col md="1" class="border-b">{{ room.booking_id }}</v-col>
            <v-col md="2" class="border-b">{{ room.purpose }}</v-col>
            <v-col md="2" class="border-b">{{ room.booking_date.slice(0, 10) }}</v-col>
            <v-col md="2" class="border-b">{{ room.start_time }}</v-col>
            <v-col md="2" class="border-b">{{ room.end_time }}</v-col>
            <v-col md="2" class="border-b">
              <div class="flex justify-center align-center mb-3" 
                   :class="{
                     'bg-yellow-400': room.status === 'pending',
                     'bg-red-400': room.status === 'cancel',
                     'bg-green-400': room.status === 'confirm'
                   }" 
                   style="width: 90px; height: 30px;">
                {{ room.status }}
              </div>
            </v-col>
            <v-col md="1" class="border-b "  >
              <v-btn @click="openDialog(room)">ดูข้อมูล</v-btn>
            </v-col>
          </v-row>
        </v-card-text>
        <div class="flex justify-end pb-5 mr-5">
        <v-btn :disabled="currentPage === 1" @click="currentPage--" class="mr-10"> Previous <<<</v-btn>
        <v-btn :disabled="currentPage === maxPage" @click="currentPage++">>>>Next</v-btn>
      </div>
      </v-card>

      
    </div>

    <v-dialog v-model="dialog" max-width="600px">
  <v-card>
    <v-card-title class="headline">{{ selectedRoom?.purpose }}</v-card-title>
    <v-card-text>
      <div><strong>ID:</strong> {{ selectedRoom?.booking_id }}</div>
      <div><strong>หัวข้อประชุม:</strong> {{ selectedRoom?.purpose }}</div>
      <div><strong>วันที่จองไว้:</strong> {{ selectedRoom?.booking_date.slice(0, 10) }}</div>
      <div><strong>วันและเวลาที่เริ่ม:</strong> {{ selectedRoom?.start_time }}</div>
      <div><strong>วันและเวลาที่สิ้นสุด:</strong> {{ selectedRoom?.end_time }}</div>
      <div><strong>สร้างการจองเมื่อ:</strong> {{ selectedRoom?.created_at ? formatDate(selectedRoom.created_at) : 'N/A' }}</div>
      <div><strong>อัพเดทการจองเมื่อ:</strong> {{ selectedRoom?.updated_at ? formatDate(selectedRoom.updated_at) : 'N/A' }}</div>
      <div><strong>Status:</strong> {{ selectedRoom?.status }}</div>
    </v-card-text>
    <v-card-actions>
      <v-btn color="red" @click="deleteBooking">ยกเลิกการจอง</v-btn>
      <v-btn @click="dialog = false">ปิด</v-btn>
    </v-card-actions>
  </v-card>
</v-dialog>


    <div class="mx-32 flex flex-col my-28">
      <v-card elevation="16" width="250" height="300">
        <v-card-title class="bg-blue text-center">ส่วนของผู้ใช้</v-card-title>
        <div class="ml-7 mt-7">
          <a href="/home" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg">> จองห้องประชุม</a>
        </div>
        <div class="ml-7 mt-5">
          <a href="/bookinghistory" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg">> ประวัติการจอง</a>
        </div>
        <div class="ml-7 mt-5">
          <a href="/calendar" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg">> ปฏิทินการจองห้องประชุม</a>
        </div>
      </v-card>

      <v-card v-if="userRole && userRole === 'admin'" class="mx-auto my-5" elevation="16" width="250" height="300">
        <v-card-title class="bg-blue text-center">ส่วนของผู้ดูแลระบบ</v-card-title>
        <div class="ml-7 mt-7">
          <a href="/bookingcalendar" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg">> ปฏิทินการจองห้องประชุม</a>
        </div>
        <div class="ml-7 mt-5">
          <a href="/news" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg">> จัดการข่าวประกาศ</a>
        </div>
        <div  class="ml-7 mt-5">
          <a href="/editroom" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg">> จัดการข้อมูลห้องประชุม</a>
        </div>
        <div class="ml-7 mt-5">
          <a class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg">> ข้อมูลห้องประชุม</a>
        </div>
      </v-card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import Navbar from '~/layout/navbar.vue';

const searchQuery = ref('');
const rooms = ref<Room[]>([]);
const dialog = ref(false);
const selectedRoom = ref<Room | null>(null); // State for the selected room
const currentPage = ref(1); // State for the current page
const itemsPerPage = 7; // Items to display per page

interface Room {
  booking_id: number;
  purpose: string;
  booking_date: string;
  start_time: string;
  end_time: string;
  status: string;
  created_at: string;
  updated_at: string;
}

// Fetch rooms function...

const openDialog = (room: Room) => {
  selectedRoom.value = room; // Set the selected room
  dialog.value = true; // Open the dialog
};

const userRole = ref<string | null>(null); // Ref to store user role

const fetchRooms = async () => {
  try {
    const userData = localStorage.getItem('user');
    if (userData) {
      const parsedUserData = JSON.parse(userData);
      const userId = parsedUserData.user_id; 
      userRole.value = parsedUserData.role;

      const response = await fetch(`http://localhost:8000/meetingroomBooking/getuserbooking/${userId}`);
      const data = await response.json();

      if (data.status === "success") {
        rooms.value = data.result;
      } else {
        console.error("Failed to fetch bookings:", data.message);
      }
    } else {
      console.error('User data is not found in local storage');
    }
  } catch (error) {
    console.error("Error fetching bookings:", error);
  }
};

onMounted(() => {
  fetchRooms();
});

const filteredRooms = computed(() => {
  return rooms.value
    .filter(room => 
      room.purpose.toLowerCase().includes(searchQuery.value.toLowerCase())
    )
    .sort((a, b) => b.booking_id - a.booking_id); // เรียงจากมากไปน้อย
});


// Pagination computed property
const paginatedRooms = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return filteredRooms.value.slice(start, start + itemsPerPage);
});

// Maximum number of pages
const maxPage = computed(() => {
  return Math.ceil(filteredRooms.value.length / itemsPerPage);
});

const formatDate = (dateString: string) => {
  if (!dateString) return 'N/A'; 
  const date = new Date(dateString);
  return date.toLocaleString('th-TH', {
    timeZone: 'Asia/Bangkok', // ปรับตามโซนเวลา
  });
};


const deleteBooking = async () => {
  if (selectedRoom.value) {
    const bookingId = selectedRoom.value.booking_id;

    try {
      const response = await fetch(`http://localhost:8000/meetingroomBooking/deletebooking/${bookingId}`, {
        method: 'DELETE',
      });
      const data = await response.json();

      if (data.status === 'success') {
        // Remove the deleted booking from the rooms list
        rooms.value = rooms.value.filter(room => room.booking_id !== bookingId);
        dialog.value = false; // Close the dialog
      } else {
        console.error("Failed to delete booking:", data.message);
      }
    } catch (error) {
      console.error("Error deleting booking:", error);
    }
  }
};

</script>

<style scoped>
/* Add any additional styles here */
</style>
