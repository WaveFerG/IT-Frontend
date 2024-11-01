<template>
  <Navbar />

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
  
        <v-card v-if="useraaa && useraaa.role === 'admin'"  elevation="16" width="280" height="1000">
        <v-card-title class="bg-blue text-center">ส่วนของผู้ดูแลระบบ</v-card-title>
        <div class="ml-5 mt-7">
          <a href="/bookingcalendar" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg"><v-icon>mdi-chevron-right</v-icon> จัดการการจองในปฏิทิน</a>
        </div>
        <div class="ml-5 mt-5">
          <a href="/news" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg"><v-icon>mdi-chevron-right</v-icon> จัดการข่าวประกาศ</a>
        </div>
        <div  class="ml-5 mt-5">
          <a href="/editroom" class="hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg"><v-icon>mdi-chevron-right</v-icon> จัดการข้อมูลห้องประชุม</a>
        </div>
        
      </v-card>
      </div>

      <div class="flex bg-gray-200 min-h-screen">
      
<!-- Form for creating news, displayed only when no news exists -->
<v-card v-if="newsList.length === 0" class="mx-auto mt-6 mb-8 p-6" width="600" height="fit-content" elevation="8">
  <v-card-title class="text-lg font-semibold text-gray-800">สร้างข่าวสารใหม่</v-card-title>
  <v-divider></v-divider>
  <v-card-text>
    <v-form ref="form" v-model="valid">
      <v-text-field
        v-model="newsData.title"
        label="หัวข้อข่าว"
        :rules="[v => !!v || 'หัวข้อข่าวจำเป็น']"
        required
        outlined
      />
      <v-textarea
        v-model="newsData.content"
        label="เนื้อหาข่าว"
        :rules="[v => !!v || 'เนื้อหาข่าวจำเป็น']"
        required
        outlined
      />
      <v-text-field
        v-model="newsData.published_date"
        label="วันที่เผยแพร่"
        :rules="[v => !!v || 'วันที่เผยแพร่จำเป็น']"
        readonly
        outlined
      />
      <v-text-field
        v-model="newsData.author_name"
        label="ชื่อผู้เขียน"
        :rules="[v => !!v || 'ชื่อผู้เขียนจำเป็น']"
        required
        outlined
      />
      <v-btn @click="createNews" color="primary" class="mt-4" block>สร้างข่าวสาร</v-btn>
      <v-alert v-if="errorMessage" type="error" dismissible class="mt-4">
        {{ errorMessage }}
      </v-alert>
    </v-form>
  </v-card-text>
</v-card>

<!-- News list with delete button aligned to the right -->
<v-card v-else class="mx-auto mt-6 mb-8 p-6" width="600"  height="fit-content"  elevation="8">
  <v-card-title class="text-xl font-semibold " style="border-bottom: 1px solid #D3D3D3;">ข่าวสารที่สร้าง</v-card-title>
<v-divider></v-divider>
<v-card-text>
  <v-list>
    <v-list-item-group>
      <v-list-item v-for="news in newsList" :key="news.news_id" class="d-flex justify-space-between mb-4">
        <v-list-item-content>
          <p class="font-semibold text-gray-800 mb-4 text-xl">หัวข้อข่าว: {{ news.title }}</p>
          <p class="text-gray-700 mb-3 font-medium text-lg">เนื้อหาข่าว: {{ news.content }}</p>
          <p class="text-gray-700 mb-3 font-medium text-lg">วันที่เผยแพร่: {{ news.published_date.slice(0,10) }}</p>
          <p class="text-gray-700 mb-4 font-medium text-lg">ชื่อผู้เขียน: {{ news.author_name }}</p>
        </v-list-item-content>

          <!-- Delete button aligned to the right -->
          <v-btn icon @click="deleteNews(news.news_id)" color="error">
            <v-icon>mdi-delete</v-icon>
          </v-btn>
        </v-list-item>
      </v-list-item-group>
    </v-list>
  </v-card-text>
</v-card>
</div>

 
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import axios from 'axios';
import Navbar from '~/layout/navbar.vue';
import Swal from 'sweetalert2'; 
import { useRouter } from 'vue-router';


const router = useRouter();

const useraaa = ref<any>(null);

onMounted(async () => {
  const userData = localStorage.getItem('user');
  console.log('asdasd',userData)

  if (userData) {
    useraaa.value = JSON.parse(userData);
  }

  if (useraaa.value.role !== 'admin') {
      await Swal.fire({
        title: 'ไม่สามารถเข้าถึงได้',
        text: "คุณไม่ใช่ admin",
        icon: 'warning',
        confirmButtonText: 'ตกลง'
      });
      router.push('/home'); // Redirect to home page
    }
    
});

// Define the News type
interface News {
  news_id: number;
  title: string;
  content: string;
  published_date: string;
  is_active: boolean;
  author_name: string;
}

const today = new Date().toISOString().slice(0, 10);

const valid = ref(false);
const errorMessage = ref('');
const newsData = ref<{
  title: string;
  content: string;
  published_date: string;
  is_active: boolean;
  author_name: string;
}>({
  title: '',
  content: '',
  published_date: today,
  is_active: true,
  author_name: '',
});

const newsList = ref<News[]>([]); // Use the defined News type

const fetchNews = async () => {
  try {
    const response = await axios.get('http://localhost:8000/news/getall');
    newsList.value = response.data.result; // Save the fetched news
    console.log(response.data.result);
  } catch (error) {
    console.error('Error fetching news:', error);
  }
};

const createNews = async () => {
  if (!newsData.value.title || !newsData.value.content || !newsData.value.author_name) {
    errorMessage.value = 'กรุณากรอกข้อมูลให้ครบถ้วน'; // Show error message if fields are empty
    return;
  }

  const token = localStorage.getItem('token');

  try {
    const response = await axios.post('http://localhost:8000/news/postnew', newsData.value, {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });
    console.log(response.data);
    newsData.value = {
      title: '',
      content: '',
      published_date: today,
      is_active: true,
      author_name: '',
    };
    errorMessage.value = ''; // Clear error message on success
    await fetchNews(); // Fetch updated news list
  } catch (error) {
    console.error('Error creating news:', error);
  }
};

// Function to delete news
const deleteNews = async (news_id: number) => {
  if (!news_id) {
    console.error('News ID is undefined');
    return;
  }

  const token = localStorage.getItem('token'); // Get the token from local storage

  try {
    const response = await axios.delete(`http://localhost:8000/news/deletenew/${news_id}`, {
      headers: {
        Authorization: `Bearer ${token}`, // Include the token in the request headers
      },
    });
    console.log(response.data);
    window.location.reload();
    await fetchNews(); // Fetch the updated news list after deletion
  } catch (error) {
    console.error('Error deleting news:', error);
  }
};

onMounted(async () => {
  await fetchNews(); // Fetch news when the component is mounted
});
</script>
