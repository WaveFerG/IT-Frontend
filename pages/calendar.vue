<template>
  <Navbar />
  
  <div class="flex bg-gray-200 min-h-screen">
    <div class="fixed flex flex-col mr-10 max-w-xs ">
      <v-sheet elevation="10" class="bg-white" style="max-width: 280px; height: 400px; border-bottom: 1px solid #D3D3D3;">
        <v-btn class="mt-7 ml-5" href="/home">
        <svg color="#757575" class="h-8 w-8"  width="24" height="24" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">  <path stroke="none" d="M0 0h24v24H0z"/>  <polyline points="5 12 3 12 12 3 21 12 19 12" />  <path d="M5 12v7a2 2 0 0 0 2 2h10a2 2 0 0 0 2 -2v-7" />  <path d="M9 21v-6a2 2 0 0 1 2 -2h2a2 2 0 0 1 2 2v6" /></svg>
        <span class="text-h6 ml-2 text-gray-500">หน้าหลัก</span>
      </v-btn>
      <v-btn class="mt-7 ml-5" href="/bookinghistory">
        <svg  color="#757575" class="h-8 w-8"  fill="none" viewBox="0 0 24 24" stroke="currentColor">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-3 7h3m-3 4h3m-6-4h.01M9 16h.01"/>
       </svg>
        <span class="text-h6 ml-2 text-gray-500">ประวัติการจอง</span>
      </v-btn>
      <v-btn class="mt-7 ml-5" href="/calendar">
        <v-icon  size="30" color="#757575">mdi-calendar</v-icon>
        <span class="text-h6 ml-2 text-gray-500">ปฏิทินการจอง</span>
      </v-btn>
      </v-sheet>

      <div class="flex flex-col  ">
      <v-card elevation="16" width="280" height="1000">
        <v-card-title class="bg-blue text-center">ส่วนของผู้ใช้</v-card-title>
        <div class="ml-5 mt-7">
          <a href="/home" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg"> <v-icon>mdi-chevron-right</v-icon> จองห้องประชุม</a>
        </div>
        <div class="ml-5 mt-5">
          <a href="/bookinghistory" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg"><v-icon>mdi-chevron-right</v-icon> ประวัติการจอง</a>
        </div>
        <div class="ml-5 mt-5">
          <a href="/calendar" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg"><v-icon>mdi-chevron-right</v-icon> ปฏิทินการจองห้องประชุม</a>
        </div>
        <div class="ml-5 mt-5">
          <a href="/account" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg"><v-icon>mdi-chevron-right</v-icon> แก้ไขข้อมูลบัญชี</a>
        </div>
      </v-card>
    </div>
    </div>

    <div style="margin-left: 300px; width: 75%; height: 90vh;">
    <FullCalendar :options="calendarOptions" />
    <!-- Modal for booking details -->
    <v-dialog v-model="dialog" max-width="600px">
<v-card>
  <div class="flex justify-between">
  <v-card-title>รายละเอียดการจอง </v-card-title>
  <v-icon class="p-5" @click="dialog = false" style="cursor: pointer;">
      mdi-close
    </v-icon>
</div>
  <v-card-text>
<div class="flex justify-between">
  <!-- Booking Details Section -->
  <div >
    <p><strong>หัวข้อประชุม:</strong> {{ selectedBooking.purpose }}</p>
    <p><strong>ไอดีห้อง:</strong> {{ selectedBooking.room_id }}</p>
    <p><strong>วันที่จอง:</strong> {{ new Date(selectedBooking.booking_date).toLocaleDateString() }}</p>
    <p><strong>เวลาเริ่ม:</strong> {{ selectedBooking.start_time }}</p>
    <p><strong>เวลาสิ้นสุด:</strong> {{ selectedBooking.end_time }}</p>
    <p><strong>สร้างเมื่อ:</strong> {{ selectedBooking.created_at.slice(0, 10) }}</p>
    <p><strong>อัพเเดทเมื่อ:</strong> {{ selectedBooking.updated_at.slice(0, 10) }}</p>
  </div>

  <!-- User Information Section -->
  <div v-if="user">
      <p class="ml-10 mb-3"><strong>ผู้ใช้</strong></p>
    <p><strong>ชื่อจริง:</strong> {{ user.firstname }}</p>
    <p><strong>นามสกุล:</strong> {{ user.lastname }}</p>
    <p><strong>อีเมล:</strong> {{ user.email }}</p>
    <p><strong>เบอร์โทรศัพท์:</strong> {{ user.phone_number }}</p>
  </div>
</div>
</v-card-text>
</v-card>
</v-dialog>
  </div>
  </div>
</template>

<script setup lang="ts">
import Navbar from '~/layout/navbar.vue';
import { ref, onMounted, computed } from 'vue';
import FullCalendar from '@fullcalendar/vue3';
import dayGridPlugin from '@fullcalendar/daygrid';
import interactionPlugin from '@fullcalendar/interaction';




const loading = ref(true);
const error = ref('');
const dialog = ref(false);
const selectedBooking = ref<any>({});






const user = ref<any>(null); // Add a ref to store user data

// Fetch user data based on booking
const fetchUser = async (userId: string) => {
try {
  const response = await fetch(`http://localhost:8000/users/getusersid/${userId}`, {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
    }
  });

  const data = await response.json();
  console.log('Fetched user data:', data); // Check the fetched user data
  

  if (response.ok) {
    user.value = data.result; // Store user data for display
  } else {
    console.error('Failed to fetch user data:', data.message);
  }
} catch (err) {
  console.error('Error fetching data:', String(err));
}
};


const fetchBookings = async () => {
  try {
    const response = await fetch('http://localhost:8000/meetingroomBooking/getbooking', {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json'
      }
    });
    const data = await response.json();

    if (data.status === 'success') {
      bookings.value = data.result;
    } else {
      error.value = 'Failed to fetch bookings.';
    }
  } catch (err) {
    error.value = 'Error fetching data: ' + String(err);
  } finally {
    loading.value = false;
  }
};

onMounted(fetchBookings);

const handleEventClick = (info: any) => {
selectedBooking.value = info.event.extendedProps; 


const userId = selectedBooking.value.user_id; 


if (!userId) {
console.warn('No user ID found for this booking. Please check the booking data.');
} else {
fetchUser(userId);
}

dialog.value = true; 
};



interface Booking {
 booking_id: string;
 room_id: string;
 purpose: string;
 booking_date: string;
 start_time: string;
 end_time: string;
 created_at: string;
 updated_at: string;
 user_id: string;
}

const bookings = ref<Booking[]>([]);

const calendarOptions = computed(() => ({
  plugins: [dayGridPlugin, interactionPlugin],
  initialView: 'dayGridMonth',
  events: bookings.value.map(booking => ({
      title: `Room ${booking.room_id}  เวลา ${booking.start_time}-${booking.end_time}`,
      start: booking.booking_date,
      end: booking.end_time,
      allDay: true,
      extendedProps: {
          purpose: booking.purpose,
          booking_id: booking.booking_id,
          room_id: booking.room_id,
          booking_date: booking.booking_date,
          start_time: booking.start_time,
          end_time: booking.end_time,
          created_at: booking.created_at,
          updated_at: booking.updated_at,
          user_id: booking.user_id // Make sure user_id is included
      }
  })),
  eventClick: handleEventClick
}));



</script>

<style scoped>
/* Add any additional styles here */
</style>

