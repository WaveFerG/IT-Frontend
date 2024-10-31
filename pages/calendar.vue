<template>

    <Navbar />
    
    <div class="flex bg-gray-200 min-h-full ">
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
    </div>
    
    
    </div>
    
  </template>
  
  <script setup lang="ts">
  import { ref, onMounted, computed } from 'vue';
  import FullCalendar from '@fullcalendar/vue3';
  import dayGridPlugin from '@fullcalendar/daygrid';
  import interactionPlugin from '@fullcalendar/interaction';
  import Navbar from '~/layout/navbar.vue';

  const bookings = ref([]);
  const loading = ref(true);
  const error = ref('');
  
  // Fetch confirmed bookings
  const fetchConfirmedBookings = async () => {
    try {
      const response = await fetch('http://localhost:8000/meetingroomBooking/getbooking', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      });
      const data = await response.json();

  
      if (data.status === 'success') {
        bookings.value = data.result; 
        console.log('allbooking',data.result)
      } else {
        error.value = 'Failed to fetch confirmed bookings.';
      }
    } catch (err) {
      error.value = 'Error fetching data: ' + String(err);
    } finally {
      loading.value = false;
    }
  };
  
  onMounted(fetchConfirmedBookings);
  
  const calendarOptions = computed(() => ({
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    events: bookings.value.map((booking:any )=> ({
      title: `Room ${booking.room_id}  เวลา ${booking.start_time}-${booking.end_time}`,
      start: booking.booking_date,
      end: new Date(new Date(booking.booking_date).setHours(new Date(booking.booking_date).getHours() + getDuration(booking.start_time, booking.end_time))),
      allDay: true,
    })),
  }));
  
  // Function to calculate the duration between start and end time
  const getDuration = (start: string, end: string) => {
    const startTime = new Date(`1970-01-01T${start}`);
    const endTime = new Date(`1970-01-01T${end}`);
    return (endTime.getTime() - startTime.getTime()) / 1000 / 60 / 60; // Return duration in hours
  };
  </script>
  
  <style scoped>
  /* Add any additional styles here */
  .my-calendar {
  background-color: #f0f0f0; /* สีพื้นหลัง */
  border: 2px solid #4CAF50; /* ขอบ */
  border-radius: 8px; /* มุมมน */
  padding: 20px; /* ระยะห่างภายใน */
}

.my-calendar .v-calendar-header {
  background-color: #4CAF50; /* สีหัว */
  color: white; /* สีตัวอักษรในหัว */
}

.my-calendar .v-calendar-day {
  color: #333; /* สีตัวอักษรวัน */
  font-weight: bold; /* ตัวหนา */
}
  </style>