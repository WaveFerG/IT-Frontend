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
  
      <!-- <div style="margin-left: 800px;">
        <h1>ปฏิทินการจองห้องประชุม</h1>
        <div v-if="loading">Loading...</div>
        <div v-if="error" class="text-red-500">Error: {{ error }}</div>
        <div v-if="bookings && bookings.length > 0">
          <ul>
            <li v-for="booking in bookings" :key="booking.booking_id">
              <strong>Room ID:</strong> {{ booking.room_id }}<br>
              <strong>Booking Date:</strong> {{ new Date(booking.booking_date).toLocaleDateString() }}<br>
              <strong>Start Time:</strong> {{ booking.start_time }}<br>
              <strong>End Time:</strong> {{ booking.end_time }}<br>
              <strong>Purpose:</strong> {{ booking.purpose }}<br>
              <hr />
            </li>
          </ul>
        </div>
        <div v-else>No bookings found.</div>
      </div> -->
  
      <div style="margin-left: 300px; width: 75%; height: 90vh;">
      <FullCalendar :options="calendarOptions" />
      <!-- Modal for booking details -->
      <v-dialog v-model="dialog" max-width="600px">
  <v-card>
    <div class="flex justify-between">
    <v-card-title>Booking Details </v-card-title>
    <v-icon class="p-5" @click="dialog = false" style="cursor: pointer;">
        mdi-close
      </v-icon>
  </div>
    <v-card-text>
  <div class="flex justify-between">
    <!-- Booking Details Section -->
    <div >
      <p><strong>Purpose:</strong> {{ selectedBooking.purpose }}</p>
      <p><strong>Booking ID:</strong> {{ selectedBooking.booking_id }}</p>
      <p><strong>Room ID:</strong> {{ selectedBooking.room_id }}</p>
      <p><strong>Booking Date:</strong> {{ new Date(selectedBooking.booking_date).toLocaleDateString() }}</p>
      <p><strong>Start Time:</strong> {{ selectedBooking.start_time }}</p>
      <p><strong>End Time:</strong> {{ selectedBooking.end_time }}</p>
      <p><strong>Created_at:</strong> {{ selectedBooking.created_at.slice(0, 10) }}</p>
      <p><strong>Updated_at:</strong> {{ selectedBooking.updated_at.slice(0, 10) }}</p>
    </div>

    <!-- User Information Section -->
    <div v-if="user">
        <p class="ml-10 mb-3"><strong>User</strong></p>
        <p><strong>User Id:</strong> {{ user.user_id }}</p>
      <p><strong>First Name:</strong> {{ user.firstname }}</p>
      <p><strong>Last Name:</strong> {{ user.lastname }}</p>
      <p><strong>Username:</strong> {{ user.username }}</p>
      <p><strong>Email:</strong> {{ user.email }}</p>
      <p><strong>Phone:</strong> {{ user.phone_number }}</p>
      <p><strong>Role:</strong> {{ user.role }}</p>
    </div>
  </div>
</v-card-text>
<v-card-actions>
  
  <v-btn color="green" @click="confirmBooking(selectedBooking.booking_id)">Confirm Booking</v-btn> <!-- ปุ่ม Confirm -->
  <v-btn color="red" @click="cancleBooking(selectedBooking.booking_id)">Cancel Booking</v-btn> <!-- ปุ่ม Cancel -->
</v-card-actions>
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
  import Swal from 'sweetalert2'; 
 
  

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
      const token = localStorage.getItem('token'); 
      const response = await fetch('http://localhost:8000/meetingroomBooking/admin/pending-booking', {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${token}` // Include the token from local storage
        }
      });
      const data = await response.json();
  
      if (data.status === 'success') {
        bookings.value = data.bookings;
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
  selectedBooking.value = info.event.extendedProps; // Get booking details from event's extendedProps

  // Retrieve user_id from the selected booking. Ensure you have this field in your booking data.
  const userId = selectedBooking.value.user_id; // Make sure 'user_id' exists in your booking data


  if (!userId) {
  console.warn('No user ID found for this booking. Please check the booking data.');
} else {
  fetchUser(userId);
}

  dialog.value = true; // Open the modal
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
        title: `Room ${booking.room_id},  ${booking.purpose}`,
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




const confirmBooking = async (bookingId: string) => {
  try {
    const token = localStorage.getItem('token');
    const response = await fetch(`http://localhost:8000/meetingroomBooking/admin/confrim-booking/${bookingId}`, {
      method: 'PATCH',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify({ purpose: 'confirm' })
    });

    const data = await response.json();
    if (response.ok) {
      Swal.fire({
        icon: 'success',
        title: 'Booking Confirmed',
        text: 'The booking has been confirmed successfully!',
        timer: 2000,
        showConfirmButton: false,
      });
      dialog.value = false; // Close the dialog
      fetchBookings(); // Refresh the bookings
    } else {
      Swal.fire({
        icon: 'error',
        title: 'Error',
        text: `Failed to confirm the booking: ${data.message}`,
      });
    }
  } catch (error) {
    Swal.fire({
      icon: 'error',
      title: 'Error',
      text: `An error occurred: ${String(error)}`,
    });
  }
};

const cancleBooking = async (bookingId: string) => {
  try {
    const token = localStorage.getItem('token');
    const response = await fetch(`http://localhost:8000/meetingroomBooking/admin/cancel-booking/${bookingId}`, {
      method: 'PATCH',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify({ purpose: 'cancel' })
    });

    const data = await response.json();
    if (response.ok) {
      Swal.fire({
        icon: 'success',
        title: 'Booking Cancelled',
        text: 'The booking has been cancelled successfully!',
        timer: 2000,
        showConfirmButton: false,
      });
      dialog.value = false; // Close the dialog
      fetchBookings(); // Refresh the bookings
    } else {
      Swal.fire({
        icon: 'error',
        title: 'Error',
        text: `Failed to cancel the booking: ${data.message}`,
      });
    }
  } catch (error) {
    Swal.fire({
      icon: 'error',
      title: 'Error',
      text: `An error occurred: ${String(error)}`,
    });
  }
};

  </script>
  
  <style scoped>
  /* Add any additional styles here */
  </style>
  