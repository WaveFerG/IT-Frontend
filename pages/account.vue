<template>
    <Navbar />
  
    <div class="flex ">
      <div class="flex flex-col mr-10 max-w-xs">
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
      
  
      <div class="flex flex-col " style="width: 800px;">
        <div class="mt-10 ml-10">
          <v-sheet elevation="5" class="mt-5" >
            <v-card-title class="border-b-md mt-1"><div class="ml-5 mt-2"><v-icon class="mb-2">
                
                mdi-account</v-icon>การตั้งค่าบัญชี
            </div>
            </v-card-title>
            <v-card-text>
              <div class="flex border-b"><v-col cols="5" class="font-bold">ชื่อผู้ใช้ :</v-col>
                <v-col>{{ user.username }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="5" class="font-bold">ชื่อ : </v-col>
                <v-col>{{ user.firstname }} {{ user.lastname }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="5" class="font-bold">อีเมล :</v-col>
                <v-col>{{ user.email }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="5" class="font-bold">เบอร์โทรศัพท์ :</v-col>
                <v-col>{{ user.phone_number }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="5" class="font-bold">ตำแหน่ง :</v-col>
                <v-col>{{ user.department }}</v-col>
              </div>
              <div class="">
              <v-btn color="#3399FF" class="mt-5 ml-3 " @click="openEditDialog">แก้ไขบัญชี</v-btn> 
              <v-btn 
  class=" mt-5 ml-10 " 
  color="#FF5252" 
  @click="logout" 
  elevation="2" 
  style="width: 100px;"
  href="/login">

  <v-icon left>mdi-logout</v-icon>
  Logout
</v-btn>
</div>

            </v-card-text>
          </v-sheet>
          

          
        </div>
      </div>

      <div class="ml-32  mt-16 ">
      <v-card elevation="16" width="250" height="300">
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
      <v-card v-if="user.role && user.role === 'admin'" class="mx-auto my-5" elevation="16" width="250" height="300">
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
    
    </div>
    <v-dialog v-model="editDialog" width="500">
    <v-card>
      <v-card-title>
        <span class="text-h6">ตั้งค่าบัญชี</span>
      </v-card-title>
      <v-card-text>
        <v-text-field
          v-model="editUser.firstname"
          label="ชื่อจริง"
        ></v-text-field>
        <v-text-field
          v-model="editUser.lastname"
          label="นามสกุล"
        ></v-text-field>
        <v-text-field
          v-model="editUser.email"
          label="อีเมล"
          type="email"
        ></v-text-field>
        <v-text-field
          v-model="editUser.phone_number"
          label="เบอร์โทรศัพท์"
          type="tel"
        ></v-text-field>
        <v-text-field
          v-model="editUser.department"
          label="ตำแหน่งงาน"
        ></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="updateUser">Save</v-btn>
        <v-btn @click="editDialog = false">Cancel</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
  
 



  </template>
  
  <script setup lang="ts">
  import { ref, onMounted } from 'vue';
  import Navbar from '~/layout/navbar.vue';
  import axios from 'axios';
  import Swal from 'sweetalert2';
  
  // User data
  const user = ref({
    username: '',
    firstname: '',
    lastname: '',
    email: '',
    phone_number: '',
    department: '',
    role: ''
  });
  
  // Edit user data
  const editUser = ref({
    username: '',
    firstname: '',
    lastname: '',
    email: '',
    phone_number: '',
    department: ''
  });
  
  // Edit dialog state
  const editDialog = ref(false);
  
  // Fetch user data function
  const fetchUserData = async (id: string) => {
    try {
      const response = await axios.get(`http://localhost:8000/users/getusersid/${id}`);
      user.value = response.data.result;
      editUser.value = { ...response.data.result };
    } catch (error) {
      Swal.fire('Error', 'Error fetching user data', 'error');
    }
  };
  
  // Update user function
  const updateUser = async () => {
    const userData = localStorage.getItem('user');
  
    if (userData) {
      const parsedUserData = JSON.parse(userData);
      const userId = parsedUserData.user_id;
  
      try {
        const response = await axios.patch(`http://localhost:8000/users/updateusers/${userId}`, editUser.value);
        user.value = { ...editUser.value };
        editDialog.value = false;
  
        Swal.fire({
          icon: 'success',
          title: 'Success',
          text: response.data.message,
          timer: 1500,
          showConfirmButton: false
        });
  
        // Add a short delay before reloading
        await new Promise(resolve => setTimeout(resolve, 500));
        window.location.reload();
      } catch (error: any) {
        const errorMessage = error.response && error.response.data.errors
          ? error.response.data.errors.map((err: { msg: any }) => err.msg).join(', ')
          : 'An unexpected error occurred. Please try again.';
          editDialog.value = false;
  
        Swal.fire({
          icon: 'error',
          title: 'Error',
          text: errorMessage
        });
      }
    }
  };
  
  // Show dialog to edit account
  const openEditDialog = () => {
    editUser.value = { ...user.value };
    editDialog.value = true;
  };
  
  // Logout function
  const logout = () => {
    Swal.fire({
      title: 'Logout',
      text: 'Are you sure you want to logout?',
      icon: 'warning',
      showCancelButton: true,
      confirmButtonColor: '#FF5252',
      confirmButtonText: 'Yes, logout'
    }).then((result) => {
      if (result.isConfirmed) {
        localStorage.removeItem('user');
        localStorage.removeItem('token');
        window.location.href = '/login';
      }
    });
  };
  
  // Fetch user data when component is mounted
  onMounted(() => {
    const userData = localStorage.getItem('user');
  
    if (userData) {
      const parsedUserData = JSON.parse(userData);
      const userId = parsedUserData.user_id;
  
      if (userId) {
        fetchUserData(userId);
      }
    }
  });
  </script>
  
  
  <style scoped>
  /* Add any additional styles here */
  </style>
  