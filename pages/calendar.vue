<template>

    <Navbar />
    
    <div class="flex bg-gray-200">
      <div class="fixed flex flex-col mr-10 max-w-xs ">
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
  
        <v-sheet elevation="10" class="bg-white flex-auto" style="max-width: 280px; height: 100vh;">
          <div class="p-2 ml-5 text-gray-500"> 
            Contact us
          </div>
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
      const response = await fetch('http://localhost:8000/meetingroomBooking/get-confirmed-booking', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      });
      
      const data = await response.json();
  
      if (data.status === 'success') {
        bookings.value = data.result; // Store the confirmed bookings
      } else {
        error.value = 'Failed to fetch confirmed bookings.';
      }
    } catch (err) {
      error.value = 'Error fetching data: ' + err.message;
    } finally {
      loading.value = false;
    }
  };
  
  onMounted(fetchConfirmedBookings);
  
  const calendarOptions = computed(() => ({
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    events: bookings.value.map(booking => ({
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