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

      <v-sheet elevation="10" class="bg-white flex-auto" style="max-width: 280px; height: 100dvh;">
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

    <div class="flex flex-col ml-80" style="width: 900px;">
      <div class="flex">
        <v-menu offset-y>
          <template v-slot:activator="{ props }">
            <v-btn v-bind="props" class="mt-10 ml-10">
              <v-icon>mdi-home</v-icon>
              ขนาดห้อง v
            </v-btn>
          </template>
          <v-list>
            <v-list-item>
              <v-checkbox label="ขนาดเล็ก" v-model="selectedSizes.small"></v-checkbox>
            </v-list-item>
            <v-list-item>
              <v-checkbox label="ขนาดกลาง" v-model="selectedSizes.medium"></v-checkbox>
            </v-list-item>
            <v-list-item>
              <v-checkbox label="ขนาดใหญ่" v-model="selectedSizes.large"></v-checkbox>
            </v-list-item>
          </v-list>
        </v-menu>

        <v-menu offset-y>
  <template v-slot:activator="{ props }">
    <v-btn v-bind="props" class="mt-10 ml-4">
      <v-icon>mdi-home</v-icon>
      อุปกรณ์ v
    </v-btn>
  </template>
  <v-list>
    <v-list-item>
      <v-checkbox label="Internet" v-model="selectedEquipments.internet"></v-checkbox>
    </v-list-item>
    <v-list-item>
      <v-checkbox label="Microphone" v-model="selectedEquipments.microphone"></v-checkbox>
    </v-list-item>
    <v-list-item>
      <v-checkbox label="Projector" v-model="selectedEquipments.projector"></v-checkbox>
    </v-list-item>
    <v-list-item>
      <v-checkbox label="White board" v-model="selectedEquipments.whiteboard"></v-checkbox>
    </v-list-item>
    <v-list-item>
      <v-checkbox label="Power outlet" v-model="selectedEquipments.poweroutlet"></v-checkbox>
    </v-list-item>
  </v-list>
</v-menu>

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

      <div class="mt-5">
        <v-container>
          <v-row>
            <v-col v-for="room in filteredRooms" :key="room.room_id" class="mb-5" cols="12">
              <v-card class="rounded-xl overflow-hidden shadow-lg" style="display: flex; height: auto;">
                <div class="m-5" style="width: 500px; height: 320px">
                  <v-img :src="`http://localhost:8000/${room.room_image}`" height="100%" width="100%" cover />
                </div>
                <div class="flex-1 pt-5">
                  <v-card-title class="text-lg font-semibold">{{ room.room_name }}</v-card-title>
                  <h1 class="mb-5">ขนาดบรรจุ: {{ room.capacity }}<br></h1>
                  <h1 class="mb-5">ที่ตั้ง: {{ room.location }}<br></h1>
                  <h1 class="mb-5">สิ่งอำนวยความสะดวก: {{ room.amenities }}<br></h1>
                  <div style="margin-top:75px;">
                    <v-btn class="m-3" color="#3399FF" @click="openDialog(room)">จองห้องนี้</v-btn>
                    <v-btn color="#778899" class="text-white" @click="fetchRoomDetails(room.room_id)">รายละเอียดเพิ่มเติม</v-btn>

                  </div>
                </div>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </div>
    </div>  
    
    <v-dialog v-model="showRoomDetailsDialog" max-width="1000px">
  <v-card>
    <v-card-title class="text-xl font-bold">
      รายละเอียดห้องประชุม
    </v-card-title>
    <v-card-text>
      <div v-if="roomDetails" class="d-flex ">
        <v-img v-for="detail in roomDetails" :key="detail.detail_id" :src="`http://localhost:8000/${detail.image_url}`" height="200" width="200" class="ma-2" />
        
        
      </div>
      
       
      <div v-else>
        <p>กำลังโหลดรายละเอียด...</p>
      </div>

      <p class="p-10"> <span class="font-semibold">รายละเอียดห้อง : </span> {{ roomDetails[0].description }}</p>

    </v-card-text>
    <v-card-actions>
      <v-btn @click="showRoomDetailsDialog = false">ปิด</v-btn>
    </v-card-actions>
  </v-card>
</v-dialog>


    <v-dialog v-model="showDialog" max-width="600px">
  <v-card class="rounded-lg shadow-lg">
    <v-card-title class="text-xl font-bold border-b-2 border-gray-300 p-4">
      {{ selectedRoom?.room_name }}
    </v-card-title>
    <v-card-text class="p-4">

      <p class="text-gray-700"><strong>หัวข้อประชุม:</strong></p>
      <v-text-field
        v-model="purpose"
        label="หัวข้อประชุม"
        placeholder="กรุณากรอกหัวข้อประชุม"
      />

      <v-divider class="my-4"></v-divider>
     
      <div class="flex justify-between">
  <!-- The date picker -->
  <v-date-picker 
  v-model="bookingDate" 
  :min="minDate"
  class="mr-8"
></v-date-picker>

<div v-if="confirmedForSelectedDate.length" class="text-red-600 mt-36" style="height: fit-content; background-color:#eeeeee">
  <p>เวลาที่จองไปแล้วในวันนี้:</p>
  <ul>
    <li v-for="booking in confirmedForSelectedDate" :key="booking.booking_date">
      {{ booking.start_time }} - {{ booking.end_time }} 
    </li>
  </ul>
</div>
</div>

      <p class="text-gray-700 mt-4"><strong>เลือกเวลาเริ่ม:</strong></p>
      <div class="flex">
        <v-select
          v-model="bookingStartHour"
          :items="hours"
          label="ชั่วโมง"
          class="mr-2"
        ></v-select>
        <v-select
          v-model="bookingStartMinute"
          :items="minutes"
          label="นาที"
        ></v-select>
      </div>

      <p class="text-gray-700 mt-4"><strong>เลือกเวลาสิ้นสุด:</strong></p>
      <div class="flex">
        <v-select
          v-model="bookingEndHour"
          :items="filteredEndHours"
          label="ชั่วโมง"
          class="mr-2"
        ></v-select>
        <v-select
          v-model="bookingEndMinute"
          :items="minutes"
          label="นาที"
        ></v-select>
      </div>

      <p class="text-gray-700 mt-2">คุณเลือกเวลาเริ่มต้น: {{ bookingStartHour }}:{{ bookingStartMinute }}</p>
      <p class="text-gray-700 mt-2">คุณเลือกเวลาสิ้นสุด: {{ bookingEndHour }}:{{ bookingEndMinute }}</p>
    </v-card-text>
    <v-card-actions class="flex justify-end p-4">
      <v-btn 
        @click="confirmBooking" 
        class="mr-2 bg-green-500 text-white font-semibold py-2 px-4 rounded-lg shadow hover:bg-green-800 transition duration-200"
        style="background-color: #48bb78;"
      >
        คอนเฟิร์มการจอง
      </v-btn>
      <v-btn 
        @click="showDialog=false" 
        class="bg-red-500 text-white font-semibold py-2 px-4 rounded-lg shadow hover:bg-red-800 transition duration-200"
        style="background-color: #f56565;"
      >
        ยกเลิก
      </v-btn>
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

      <v-card v-if="user.role && user.role === 'admin'" class="mx-auto my-5" elevation="16" width="250" height="300">
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
import { ref, onMounted, computed } from 'vue';
import Navbar from '~/layout/navbar.vue';
import axios from 'axios';
import Swal from 'sweetalert2';


const selectedSizes = ref({ small: false, medium: false, large: false });
const selectedEquipments = ref({
  internet: false,
  microphone: false,
  projector: false,
  whiteboard: false,
  poweroutlet: false,
});

interface Room {
  room_id: string;
  room_name: string;
  capacity: number; // Use number for capacity
  location: string;
  amenities: string;
  is_available: boolean;
  room_image: string;
}

const rooms = ref<Room[]>([]);

const filteredRooms = computed(() => {
  // ฟิลเตอร์ตามขนาดห้องที่เลือก
  const filteredBySize = rooms.value.filter(room => {
    const matchesSize = 
      (!selectedSizes.value.small && !selectedSizes.value.medium && !selectedSizes.value.large) || // ถ้าไม่ได้เลือกขนาดใดเลย ให้แสดงทุกขนาด
      (selectedSizes.value.small && room.capacity < 21) ||
      (selectedSizes.value.medium && room.capacity >= 21 && room.capacity <= 30) ||
      (selectedSizes.value.large && room.capacity > 30);

    return matchesSize;
  });

  // ฟิลเตอร์ตามอุปกรณ์ที่เลือก
  const filteredByEquipment = filteredBySize.filter(room => {
    const matchesEquipments = 
      (!selectedEquipments.value.internet || room.amenities.includes("Internet")) &&
      (!selectedEquipments.value.microphone || room.amenities.includes("Microphone")) &&
      (!selectedEquipments.value.projector || room.amenities.includes("Projector")) &&
      (!selectedEquipments.value.whiteboard || room.amenities.includes("White board")) &&
      (!selectedEquipments.value.poweroutlet || room.amenities.includes("Power outlet"));

    return matchesEquipments;
  });

  // ฟิลเตอร์ตามคำค้นหา
  return filteredByEquipment.filter(room =>
    room.room_name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});



const user = ref({
    user_id:'',
    username: '',
    role: '',
});

const fetchUserData = async (id:any) => {
  try {
    const response = await axios.get(`http://localhost:8000/users/getusersid/${id}`);
    user.value = response.data.result; // Set the user data
    console.log(response.data.result)
  } catch (error) {
    console.error('Error fetching user data:', error);
  }
};

onMounted(async () => {
  const userData = localStorage.getItem('user');
  
  if (userData) {
    const parsedUserData = JSON.parse(userData);
    const userId = parsedUserData.user_id; // Extract user_id from parsed data
    console.log(userId)
    
    if (userId) {
      fetchUserData(userId); // Fetch user data using the user_id
    } else {
      console.error('User ID not found in user data');
    }
  } else {
    console.error('User data is not found in local storage');
    
  }


    try {
        const roomsResponse = await axios.get('http://localhost:8000/meetingrooms/getmeetingroom');
        rooms.value = roomsResponse.data; 
        console.log(roomsResponse.data);
    } catch (error) {
        console.error('Error fetching meeting rooms:', error);
    }
    
});



const showDialog = ref(false);
const selectedRoom = ref<Room | null>(null);

const openDialog = (room: Room) => {
  selectedRoom.value = room;
  showDialog.value = true;
};





const searchQuery = ref('');

const purpose = ref(''); // New reactive property for meeting topic

const bookingDate = ref(null);
const bookingStartHour = ref(null);
const bookingStartMinute = ref(null);
const bookingEndHour = ref(null);
const bookingEndMinute = ref(null);
const minDate = ref(new Date().toISOString().substr(0, 10)); // วันที่ขั้นต่ำเป็นวันนี้

const hours = ref(Array.from({ length: 17 }, (_, i) => (i + 6 < 10 ? `0${i + 6}` : `${i + 6}`)));
const minutes = ref(['00', '15', '30', '45']); // นาทีที่เลือกได้

const formattedBookingDate = computed(() => {
  if (bookingDate.value) {
    const date = new Date(bookingDate.value);
    const day = String(date.getDate()).padStart(2, '0'); // เพิ่ม 0 ข้างหน้าเมื่อวันน้อยกว่า 10
    const month = String(date.getMonth() + 1).padStart(2, '0'); // เดือนเริ่มจาก 0
    const year = date.getFullYear() ; // เพิ่ม 543 สำหรับปีพุทธศักราช
    return `${year}-${month}-${day}`;
  }
  return '';
});

const confirmBooking = async () => {
  if (!selectedRoom.value) return; // ตรวจสอบว่ามีห้องที่เลือกหรือไม่

  // สร้างเวลาที่จะตรวจสอบ
  const startTime = `${bookingStartHour.value}:${bookingStartMinute.value}`;
  const endTime = `${bookingEndHour.value}:${bookingEndMinute.value}`;

  // ตรวจสอบว่าเวลาเริ่มต้นน้อยกว่าหรือเท่ากับเวลาสิ้นสุด
  if (startTime >= endTime) {
    alert('กรุณากรอกเวลาให้เหมาะสมและถูกต้อง');
    return;
  }

  if (!purpose.value) {
    alert('กรุณากรอกหัวข้อประชุม');
    return;
  }

  // สร้างข้อมูลการจอง
  const bookingData = {
    user_id: user.value.user_id,
    room_id: selectedRoom.value.room_id,
    booking_date: formattedBookingDate.value,
    start_time: startTime,
    end_time: endTime,
    purpose: purpose.value,
  };

  try {
    // ส่งข้อมูลการจองไปยังเซิร์ฟเวอร์
    const response = await axios.post('http://localhost:8000/meetingroomBooking/postbooking', bookingData);
    console.log(response.data);

    // ปิด dialog ก่อน
    showDialog.value = false;

    // Reset form fields after successful booking
    purpose.value = '';
    bookingDate.value = null;
    bookingStartHour.value = null;
    bookingStartMinute.value = null;
    bookingEndHour.value = null;
    bookingEndMinute.value = null;

    // แสดง SweetAlert แจ้งเตือนการจองสำเร็จ
    await Swal.fire({
      title: 'สำเร็จ!',
      text: 'การจองของคุณสำเร็จแล้ว!',
      icon: 'success',
      confirmButtonText: 'ตกลง'
    });

  } catch (error) {
    console.error('Error confirming booking:', error);
    alert('เวลาที่คุณเลือกมีการจองอยู่แล้วโปรดเปลี่ยนเวลาจอง');
  }
};
const filteredEndHours = computed(() => {
  if (bookingStartHour.value) {
    const startHour = parseInt(bookingStartHour.value);
    // กำหนดให้เวลาสิ้นสุดสามารถเลือกได้ตั้งแต่ชั่วโมงถัดไปจนถึง 22:00
    return hours.value.filter(hour => parseInt(hour) > startHour && parseInt(hour) <= 22);
  }
  return hours.value; // ถ้าไม่มีการเลือกเวลาเริ่มต้น ให้แสดงทั้งหมด
});



const confirmedBookings = ref([
  {
    room_id: '',
    start_time: '',
    end_time: '',
    booking_date: ''
  },
]);

const confirmedForSelectedDate = computed(() => {
  if (!bookingDate.value) return []; // ถ้าไม่มีวันที่เลือก ให้คืนค่าฐานข้อมูลว่าง
  const selectedDate = new Date(bookingDate.value).toISOString().split('T')[0];

  // กรองการจองทั้งหมดตามวันที่ที่เลือก
  const bookingsForSelectedDate = confirmedBookings.value.filter(booking =>
    booking.booking_date.split('T')[0] === selectedDate
  );

  // เรียงการจองตามเวลาเริ่มต้น
  return bookingsForSelectedDate.sort((a, b) => {
    const startA = new Date(`1970-01-01T${a.start_time}`);
    const startB = new Date(`1970-01-01T${b.start_time}`);
    return startA.getTime() - startB.getTime(); // เรียงตามเวลา
  });
});

// ฟังก์ชัน fetchConfirmedBookings ที่จะดึงข้อมูลการจองที่ยืนยันจาก API
const fetchAllBookings = async () => {
  try {
    const response = await axios.get('http://localhost:8000/meetingroomBooking/getbooking');
    confirmedBookings.value = response.data.result; // เก็บข้อมูลการจองทั้งหมด
    console.log(response.data.result);
  } catch (error) {
    console.error('Error fetching all bookings:', error);
  }
};

// เมื่อคอมโพเนนต์ถูก mounted จะเรียกใช้ฟังก์ชัน fetchConfirmedBookings
onMounted(async () => {
  await fetchAllBookings(); // ดึงข้อมูลการจองทั้งหมดแทนที่จะเป็นการจองที่ได้รับการยืนยัน
});



const roomDetails = ref(null);
const showRoomDetailsDialog = ref(false);

const fetchRoomDetails = async (roomId) => {
  try {
    const response = await axios.get(`http://localhost:8000/meetingrooms/getmeetingroomdetail/${roomId}`);
    if (response.data.status === "success") {
      roomDetails.value = response.data.result;
      console.log('asdasd',response.data.result);
      showRoomDetailsDialog.value = true;
    } else {
      alert('ไม่พบรายละเอียดสำหรับห้องประชุมนี้');
    }
  } catch (error) {
    console.error('Error fetching room details:', error);
    alert('เกิดข้อผิดพลาดในการดึงข้อมูล');
  }
};





</script>

<style scoped>
/* Add any additional styles here */
</style>
