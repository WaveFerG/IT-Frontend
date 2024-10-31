<template>
  <div class="bg-white" style="border-bottom: 1px solid #D3D3D3; position: sticky; z-index: 50; height: 80px; top: 0;">
    <v-container fluid class="py-1">
      <v-row>
        <v-col class="flex items-center">
          <img src="/icon.jpg" class="w-16 h-16 rounded-full " alt="Logo">
          <v-col>
            <div class="font-weight-bold">
              Conference Room<br>
              <span class="text-blue-800 text-h5 font-weight-bold">Booking</span>
            </div>
          </v-col>
        </v-col>

        <v-col md="7" class="flex items-center mt-4">
          <span class="mr-3 text-h5">จองห้องประชุม,</span>
          <span class="mr-3 text-h5">{{ user.firstname }} {{ user.lastname }}</span>
          <v-btn icon href="/account" class="mr-10">
            <v-icon>mdi-pencil</v-icon>
          </v-btn>
        </v-col>

        <v-col class="flex items-center justify-end">
          <v-icon class="mr-5">mdi-help-circle</v-icon>
          <v-icon class="mr-5" @click="showDialog = true">mdi-bell</v-icon>
          <v-btn href="/account">
            <v-icon>mdi-account</v-icon>
            <span>Account</span>
          </v-btn>

          <v-btn 
  v-if="isLoggedIn" 
  class="ml-3" 
  @click="logout" 
  elevation="2" 
  style="width: 100px;"
>
  <v-icon left>mdi-logout</v-icon>
  Logout
</v-btn>

<v-btn 
  v-else 
  class="ml-3" 
  href="/login"
>
  <v-icon>mdi-login</v-icon>
  <span>login</span>
</v-btn>

        </v-col>
      </v-row>

      <!-- Dialog สำหรับการแจ้งเตือน -->
      <v-dialog v-model="showDialog" width="1200">
    <v-card class="rounded-xl" style="max-width: 1200px; height: 700px; border: 1px solid black;">
      <v-card-title class="d-flex align-center" style="border-bottom: 1px solid black;">
        <v-icon class="mr-2" size="36">mdi-bell</v-icon>
        <span class="headline">การแจ้งเตือนต่างๆในการจองห้องประชุม</span>
        <v-spacer></v-spacer>
        <v-icon class="close-icon" @click="showDialog = false">mdi-close</v-icon>
      </v-card-title>
      <div class="ml-3">
        <div class="ml-10 mr-10" style="background-color: #e5e7eb;">
          <div class="ml-3">
         <div class="ml-10 mr-10" style="background-color: #e5e7eb;">
            <h1 class="mt-4 ml-10 text-h5">สวัสดีครับคุณ <span class="text-h5">{{ user.firstname }} {{ user.lastname }}</span></h1>
            <h1 class="ml-3" style="max-width: 68rem;">
              <template v-if="newsList.length > 0">
                คุณมีข่าวสาร : 
                <span class="ml-3 font-semibold" v-for="news in newsList" :key="news.news_id">หัวข้อ : {{ news.title }}</span>
                  <ul>
      
                 <li v-for="news in newsList" :key="news.news_id" class="mt-2">รายละเอียด : {{ news.content }}</li>
                 <li v-for="news in newsList" :key="news.news_id" class="mt-2">วันที่แอดมินสร้างข่าว : {{ news.published_date.slice(0, 10)  }} <span class="ml-5" v-for="news in newsList" :key="news.news_id">ผู้เขียน : {{ news.author_name }}</span></li>
               </ul>
              </template>
             <template v-else>
              ในตอนนี้ยังไม่มีแจ้งเตือนข่าวสารใดๆ
              </template>
             </h1>
         </div>
      </div>
        </div>

      <v-col md="5" class="bg-green mt-7 ml-16 p-4">
  <h2 class="text-h6">User Bookings Confirm</h2>
  <div>
    <!-- Show only paginated bookings -->
    <ul class="list-none p-0">
      <li v-for="booking in paginatedBookings" :key="booking.booking_id" class="border-b border-gray-300 py-2 bg-white rounded-md shadow-sm">
        <div class="p-2">
          <span class="font-bold">Booking ID:</span> {{ booking.booking_id }} - {{ booking.purpose }}<br>
          <span class="font-semibold">Date:</span> {{ formatDate(booking.booking_date) }} | 
          <span class="font-semibold">Time:</span> {{ booking.start_time }} - {{ booking.end_time }}
        </div>
      </li>
    </ul>
  </div>
  
  <!-- Pagination component -->
  <v-pagination
    v-model="currentPage"
    :length="pageCount"
    :total-visible="7"
    class="mt-5"
    @input="onPageChange"
  ></v-pagination>
</v-col>


      </div>
    </v-card>
  </v-dialog>

      <!-- Dialog สำหรับแก้ไขชื่อ -->
     
    </v-container>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';
import { useRoute } from 'vue-router';

const showDialog = ref(false);
const user = ref({});
const bookings = ref([]); // Reactive variable to store bookings
const route = useRoute();

// Function to fetch user data by ID
const fetchUserData = async (id) => {
  try {
    const response = await axios.get(`http://localhost:8000/users/getusersid/${id}`);
    user.value = response.data.result; // Set the user data
  } catch (error) {
    console.error('Error fetching user data:', error);
  }
};

// Function to fetch user bookings
const fetchUserBookings = async (userId) => {
  try {
    const response = await axios.get(`http://localhost:8000/meetingroomBooking/getuserbooking/${userId}`);
    if (response.data.status === "success") {
      // กรองการจองที่มีสถานะเป็น 'confirm' และเรียงตาม booking_id จากมากไปน้อย
      bookings.value = response.data.result
        .filter(booking => booking.status === 'confirm')
        .sort((a, b) => b.booking_id - a.booking_id); // เรียง booking_id
    }
  } catch (error) {
    console.error('Error fetching user bookings:', error);
  }
};


// Show dialog only on home route
if (route.path === '/home') {
  showDialog.value = true; // Show dialog only on home
}

const isLoggedIn = ref(false);
// Fetch user data and bookings when component is mounted
onMounted(() => {
  const userData = localStorage.getItem('user');
  isLoggedIn.value = !!userData;
  
  if (userData) {
    const parsedUserData = JSON.parse(userData);
    const userId = parsedUserData.user_id; // Extract user_id from parsed data
  
    if (userId) {
      fetchUserData(userId); // Fetch user data using the user_id
      fetchUserBookings(userId); // Fetch user bookings using the user_id
    } else {
      console.error('User ID not found in user data');
    }
  } else {
    console.error('User data is not found in local storage');
  }
});

// Helper function to format booking date
const formatDate = (dateString) => {
  const options = { year: 'numeric', month: 'long', day: 'numeric' };
  return new Date(dateString).toLocaleDateString('th-TH', options); // Format date to Thai format
};



const itemsPerPage = 10; // Number of items per page
const currentPage = ref(1); // Track the current page

// Computed property for paginated bookings
const paginatedBookings = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return bookings.value.slice(start, end);
});

// Compute the total number of pages
const pageCount = computed(() => {
  return Math.ceil(bookings.value.length / itemsPerPage);
});

// Handler for page change
const onPageChange = (page) => {
  currentPage.value = page;
};



const newsList = ref([]); // Reactive variable to store news
const errorMessage = ref(''); // Variable to store error messages

// Function to fetch news data
const fetchNews = async () => {
  try {
    const response = await axios.get('http://localhost:8000/news/getall');
    newsList.value = response.data.result; // Store fetched news
  } catch (error) {
    console.error('Error fetching news:', error);
    errorMessage.value = 'ไม่สามารถดึงข้อมูลข่าวสารได้';
  }
};

// Fetch news when the component is mounted
onMounted(() => {
  fetchNews();
});


const logout = () => {
    localStorage.removeItem('user');
    localStorage.removeItem('token');
    console.log('User logged out');
    window.location.href = '/login';
  };
  
</script>

<style scoped>
</style>
