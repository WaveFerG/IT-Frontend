<template>
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
    
      <div class="flex flex-col ml-80 " style="width: 900px;">
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
    
        <v-btn @click="showForm=true" class="ml-7 mt-5 hover:bg-blue-100 hover:text-blue-800 duration-300 ease-in-out p-2 rounded-lg " style="width: 120px;">
          เพิ่มข้อมูลใหม่
        </v-btn>
    
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
                      <v-btn class="m-3" color="#3399FF" @click="editRoom(room)">แก้ไขห้อง</v-btn>
                      <v-btn @click="deleteRoom(room.room_id)" color="red" class="text-white">ลบห้อง</v-btn>
                      <v-btn  color="green" class="text-white">เพิ่มรายละเอียดห้อง</v-btn>
                    </div>
                  </div>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </div>
      </div>  
    </div>
  
    <v-dialog v-model="showEditForm" max-width="600px">
  <v-card>
    <v-card-title>
      <span class="headline">แก้ไขข้อมูลห้องประชุม</span>
    </v-card-title>
    <v-card-text>
      
      <v-text-field v-model="editRoomData.room_name" label="ชื่อห้อง" required />
      <v-text-field v-model="editRoomData.capacity" label="ความจุ" required />
      <v-text-field v-model="editRoomData.location" label="ที่ตั้ง" required />
      <v-textarea v-model="editRoomData.amenities" label="สิ่งอำนวยความสะดวก" required />

      <!-- ปุ่มเปลี่ยนรูปภาพ -->
      <div v-if="editRoomData.room_image">
        <v-img :src="`http://localhost:8000/${editRoomData.room_image}`" height="300" class="mb-2" />
      </div>
      <v-btn @click="showFileInput = !showFileInput" class="mt-2">
        {{ showFileInput ? 'ซ่อนรูปภาพ' : 'เปลี่ยนรูปภาพ' }}
      </v-btn>

      <!-- Input สำหรับเลือกไฟล์ -->
      <v-file-input 
        v-if="showFileInput"
        v-model="editRoomData.uploadfile" 
        label="เลือกรูปภาพ (ถ้าต้องการเปลี่ยน)" 
        :required="false" 
      />
    </v-card-text>
    <v-card-actions>
      <v-btn @click="updateRoom(editRoomData.room_id)" color="primary">บันทึกการแก้ไข</v-btn>
      <v-btn @click="showEditForm = false" color="red">ยกเลิก</v-btn>
    </v-card-actions>
  </v-card>
</v-dialog>



  
    <v-dialog v-model="showForm" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">เพิ่มข้อมูลห้องประชุม</span>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="newRoom.room_name" label="ชื่อห้อง" required />
          <v-text-field v-model="newRoom.capacity" label="ความจุ" required />
          <v-text-field v-model="newRoom.location" label="ที่ตั้ง" required />
          <v-textarea v-model="newRoom.amenities" label="สิ่งอำนวยความสะดวก" required />
          <v-file-input v-model="newRoom.uploadfile" label="เลือกรูปภาพ" required />
        </v-card-text>
        <v-card-actions>
          <v-btn @click="addRoom" color="primary">เพิ่มข้อมูล</v-btn>
          <v-btn @click="showForm = false" color="red">ยกเลิก</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </template>
  
  <script setup lang="ts">
  import { ref, computed, onMounted } from 'vue';
  import axios from 'axios';
  import Swal from 'sweetalert2';
  import { useRouter } from 'vue-router';

  const router = useRouter();
  const showForm = ref(false);
  const showEditForm = ref(false);
  const selectedSizes = ref({ small: false, medium: false, large: false });
  const selectedEquipments = ref({
  internet: false,
  microphone: false,
  projector: false,
  whiteboard: false,
  poweroutlet: false,
});
  
  
  interface user {
  user_id: string;
  role: string; // Add role to the user interface
  username: string;
  firstname: string;
  lastname: string;
  email: string;
  phone_number: string;
  department: string;
}

const user = ref<user>({
  user_id: '',
  role: '',
  username: '',
  firstname: '',
  lastname: '',
  email: '',
  phone_number: '',
  department: ''
});
  

const fetchUserData = async (id: any) => {
  try {
    const response = await axios.get(`http://localhost:8000/users/getusersid/${id}`);
    user.value = response.data.result; // Set the user data
    console.log(response.data.result);

    // Check user role and redirect if not admin
    if (user.value.role !== 'admin') {
      await Swal.fire({
        title: 'ไม่สามารถเข้าถึงได้',
        text: "คุณไม่ใช่ admin",
        icon: 'warning',
        confirmButtonText: 'ตกลง'
      });
      router.push('/home'); // Redirect to home page
    }
  } catch (error) {
    console.error('Error fetching user data:', error);
  }
};

// Fetch user data when component is mounted
onMounted(() => {
  const userData = localStorage.getItem('user');

  if (userData) {
    const parsedUserData = JSON.parse(userData);
    const userId = parsedUserData.user_id; // Extract user_id from parsed data
    console.log(userId);

    if (userId) {
      fetchUserData(userId); // Fetch user data using the user_id
    } else {
      console.error('User ID not found in user data');
    }
  } else {
    console.error('User data is not found in local storage');
  }
});


interface Room {
    room_id: string;
    room_name: string;
    capacity: number;
    location: string;
    amenities: string;
    is_available: boolean;
    room_image: string;
  }
  
  const rooms = ref<Room[]>([]);
  const newRoom = ref({
    room_name: '',
    capacity: '',
    location: '',
    amenities: '',
    uploadfile: null as File | null
  });

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

  
  // Fetch meeting rooms data from the API
  onMounted(async () => {
    try {
      const roomsResponse = await axios.get('http://localhost:8000/meetingrooms/getmeetingroom');
      rooms.value = roomsResponse.data; 
      console.log(roomsResponse.data)
    } catch (error) {
      console.error('Error fetching meeting rooms:', error);
    }
  });
  
  const addRoom = async () => {
  // Check for empty fields
  if (!newRoom.value.room_name || !newRoom.value.capacity || !newRoom.value.location || !newRoom.value.amenities || !newRoom.value.uploadfile) {
    showForm.value = false;
    Swal.fire({
      icon: 'warning',
      title: 'ข้อมูลไม่ครบถ้วน',
      text: 'กรุณาใส่ข้อมูลในทุกช่อง!',
    });
    return; // Stop the function if validation fails
  }

  const formData = new FormData();
  Object.keys(newRoom.value).forEach(key => {
    const value = newRoom.value[key as keyof typeof newRoom.value];
    if (key === 'uploadfile' && value) {
      formData.append(key, value as File);
    } else {
      formData.append(key, value as string);
    }
  });

  const token = localStorage.getItem('token'); // Retrieve token from localStorage

  try {
    const response = await axios.post('http://localhost:8000/meetingrooms/postmeetingroom', formData, {
      headers: {
        'Content-Type': 'multipart/form-data',
        'Authorization': `Bearer ${token}`
      }
    });

    if (response.data.status === "success") {
      showForm.value = false;
      Swal.fire({
        icon: 'success',
        title: 'เพิ่มข้อมูลสำเร็จ',
        timer: 1000,
      });
      // Refresh the page
      location.reload();
    } else {
      // Close the dialog before showing error
      showForm.value = false;

      // Show error notification
      Swal.fire({
        icon: 'error',
        title: 'เกิดข้อผิดพลาด',
        text: response.data.message || 'ข้อมูลไม่ถูกต้อง กรุณาลองใหม่',
      });
    }
  } catch (error) {
    console.error('Error adding room:', error);
    
    // Close the dialog before showing error
    showForm.value = false;

    // Show error notification for API error
    Swal.fire({
      icon: 'error',
      title: 'เกิดข้อผิดพลาด',
      text: 'ไม่สามารถเพิ่มห้องประชุมได้ กรุณาลองใหม่',
    });
  }
};

  
  interface Room {
  room_id: string;
  room_name: string;
  capacity: number;
  location: string;
  amenities: string;
  is_available: boolean;
  room_image: string;
  uploadfile: File | null; // Optional property
}

const editRoomData = ref<Room>({
  room_id: '',
  room_name: '',
  capacity: 0,
  location: '',
  amenities: '',
  is_available: false,
  room_image: '',
  uploadfile: null, // Initialize as null
});

const editRoom = (room: Room) => {
  editRoomData.value = { ...room }; // Copy selected room data, keep uploadfile
  showEditForm.value = true; // Open edit form
};


const showFileInput = ref(false);

// Update room function with correct typing
const updateRoom = async (roomId: string) => {
  const formData = new FormData();
  Object.keys(editRoomData.value).forEach(key => {
    const value = editRoomData.value[key as keyof typeof editRoomData.value];
    if (key === 'uploadfile' && value) {
      formData.append(key, value as File);
    } else if (key !== 'uploadfile') { // ไม่ต้องรวม uploadfile ถ้าเป็น null
      formData.append(key, value as string);
    }
  });

  try {
    const token = localStorage.getItem('token'); 
    const response = await axios.patch(`http://localhost:8000/meetingrooms/updatemeetingroom/${roomId}`, formData, {
      headers: {
        'Content-Type': 'multipart/form-data',
        'Authorization': `Bearer ${token}`
      }
    });
    if (response.data.status === "success") {
      const index = rooms.value.findIndex(r => r.room_id === roomId);
      
      if (index !== -1) {
        // Use the type assertion here
        rooms.value[index] = { ...rooms.value[index], ...editRoomData.value } as Room; // Ensure proper typing
      }
      showEditForm.value = false; // Close the form
    }
  } catch (error) {
    console.error('Error updating room:', error);
  }
};


const deleteRoom = async (roomId: string) => {
    const { isConfirmed } = await Swal.fire({
        title: 'ยืนยันการลบ',
        text: "คุณแน่ใจหรือไม่ว่าต้องการลบห้องประชุมนี้?",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'ใช่, ลบเลย!',
        cancelButtonText: 'ยกเลิก'
    });

    if (isConfirmed) {
        try {
          const token = localStorage.getItem('token'); 
            const response = await axios.delete(`http://localhost:8000/meetingrooms/deletemeetingroom/${roomId}`, {
            headers: {
               'Content-Type': 'multipart/form-data',
               'Authorization': `Bearer ${token}`
            }
           });
            if (response.data.status === "success") {
                rooms.value = rooms.value.filter(room => room.room_id !== roomId); // อัปเดตรายการห้องประชุม
                Swal.fire(
                    'ลบสำเร็จ!',
                    'ห้องประชุมถูกลบเรียบร้อยแล้ว.',
                    'success'
                );
            }
        } catch (error) {
            console.error('Error deleting room:', error);
            Swal.fire(
                'เกิดข้อผิดพลาด!',
                'ไม่สามารถลบห้องประชุมได้.',
                'error'
            );
        }
    }
};


  const searchQuery = ref('');
  </script>
  
  <style scoped>
  /* Add any additional styles here */
  </style>
  