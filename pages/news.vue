<template>
  <div>
    <h1 class="text-center text-2xl font-bold mb-4">จัดการข้อมูลข่าวสาร</h1>

    <!-- ฟอร์มสร้างข่าวจะแสดงเฉพาะเมื่อไม่มีข่าวในรายการ -->
    <v-card v-if="newsList.length === 0" class="mx-auto" width="500">
      <v-card-title class="text-lg font-semibold">สร้างข่าวสารใหม่</v-card-title>
      <v-card-text>
        <v-form ref="form" v-model="valid">
          <v-text-field
            v-model="newsData.title"
            label="หัวข้อข่าว"
            :rules="[v => !!v || 'หัวข้อข่าวจำเป็น']"
            required
          />
          <v-textarea
            v-model="newsData.content"
            label="เนื้อหาข่าว"
            :rules="[v => !!v || 'เนื้อหาข่าวจำเป็น']"
            required
          />
          <v-text-field
            v-model="newsData.published_date"
            label="วันที่เผยแพร่"
            :rules="[v => !!v || 'วันที่เผยแพร่จำเป็น']"
            readonly
          />
          <v-text-field
            v-model="newsData.author_name"
            label="ชื่อผู้เขียน"
            :rules="[v => !!v || 'ชื่อผู้เขียนจำเป็น']"
            required
          />
          <v-btn @click="createNews" color="primary">สร้างข่าวสาร</v-btn>
          <v-alert v-if="errorMessage" type="error" dismissible class="mt-4">
            {{ errorMessage }}
          </v-alert>
        </v-form>
      </v-card-text>
    </v-card>

    <!-- News list with delete button -->
     
    <div>

    <!-- News list with delete button on the right -->
    <v-card class="mx-auto mt-6" width="500">
      <v-card-title class="text-lg font-semibold">ข่าวสารที่สร้าง</v-card-title>
      <v-card-text>
        <v-list>
          <v-list-item-group>
            <v-list-item v-for="news in newsList" :key="news.news_id" class="d-flex justify-space-between">
              <v-list-item-content>
                <v-list-item-title class="font-semibold">{{ news.title }}</v-list-item-title>
                <v-list-item-subtitle>{{ news.content }}</v-list-item-subtitle>
                <v-list-item-subtitle>{{ news.published_date }}</v-list-item-subtitle>
                <v-list-item-subtitle v-if="news.is_active">สถานะ: เผยแพร่</v-list-item-subtitle>
                <v-list-item-subtitle v-else>สถานะ: ไม่เผยแพร่</v-list-item-subtitle>
                <v-list-item-subtitle>ผู้เขียน: {{ news.author_name }}</v-list-item-subtitle>
              </v-list-item-content>

              <!-- Delete button aligned to the right using flex -->
              <v-btn icon @click="deleteNews(news.news_id)" color="error">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-card-text>
    </v-card>
  </div>

  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import axios from 'axios';

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
