<template>
    <Navbar />
  
    <div class="flex">
      <div class="flex flex-col mr-10 max-w-xs">
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
  
      <div class="flex flex-col " style="width: 700px;">
        <div class="mt-10 ml-10">
          <v-sheet elevation="5" class="mt-5">
            <v-card-title class="border-b-md mt-1"><div class="ml-5 mt-2"><v-icon class="mb-2">
                
                mdi-account</v-icon>Account Settings
            </div>
            </v-card-title>
            <v-card-text>
              <div class="flex border-b"><v-col cols="6" class="font-bold">Username :</v-col>
                <v-col>{{ user.username }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="6" class="font-bold">Name : </v-col>
                <v-col>{{ user.firstname }} {{ user.lastname }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="6" class="font-bold">Email :</v-col>
                <v-col>{{ user.email }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="6" class="font-bold">Phonenumber :</v-col>
                <v-col>{{ user.phone_number }}</v-col>
              </div>
              <div class="flex border-b"><v-col cols="6" class="font-bold">Department :</v-col>
                <v-col>{{ user.department }}</v-col>
              </div>
              <div class="">
              <v-btn color="#3399FF" class="mt-5 ml-3 " @click="openEditDialog">Edit Account</v-btn> 
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
    </div>
    <v-dialog v-model="editDialog" width="500">
    <v-card>
      <v-card-title>
        <span class="text-h6">Edit Account</span>
      </v-card-title>
      <v-card-text>
        <v-text-field
          v-model="editUser.firstname"
          label="First Name"
        ></v-text-field>
        <v-text-field
          v-model="editUser.lastname"
          label="Last Name"
        ></v-text-field>
        <v-text-field
          v-model="editUser.email"
          label="Email"
          type="email"
        ></v-text-field>
        <v-text-field
          v-model="editUser.phone_number"
          label="Phone Number"
          type="tel"
        ></v-text-field>
        <v-text-field
          v-model="editUser.department"
          label="Department"
        ></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="updateUser">Save</v-btn>
        <v-btn @click="editDialog = false">Cancel</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
  
  <v-snackbar v-model="snackbar" :timeout="3000" color="error">
  {{ snackbarMessage }}
  <v-btn color="white" @click="snackbar = false">Close</v-btn>
</v-snackbar>
  </template>
  
  <script setup lang="ts">
  import { ref, onMounted } from 'vue';
  import Navbar from '~/layout/navbar.vue';
  import axios from 'axios';
  
  // User data
  const user = ref({
    username: '',
    firstname: '',
    lastname: '',
    email: '',
    phone_number: '',
    department: ''
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
      editUser.value = { ...response.data.result }; // Initialize editUser with current user data
    } catch (error) {
      console.error('Error fetching user data:', error);
    }
  };
  
  const snackbar = ref(false);
const snackbarMessage = ref('');
  // Update user function
  const updateUser = async () => {
  const userData = localStorage.getItem('user');
  
  if (userData) {
    const parsedUserData = JSON.parse(userData);
    const userId = parsedUserData.user_id;

    try {
      const response = await axios.patch(`http://localhost:8000/users/updateusers/${userId}`, editUser.value);
      user.value = { ...editUser.value };
      editDialog.value = false; // Close the dialog
      snackbarMessage.value = response.data.message; // Success message
      snackbar.value = true; // Open snackbar
      
      // Refresh the page to update the navbar
      window.location.reload();
    } catch (error: any) {  // Add type declaration here
      if (error.response && error.response.data.errors) {
        const messages = error.response.data.errors.map((err: { msg: any }) => err.msg).join(', '); // Declare type for err
        snackbarMessage.value = messages; // Set error messages
      } else {
        snackbarMessage.value = 'An unexpected error occurred. Please try again.';
      }
      snackbar.value = true; // Open snackbar for error
    }
  }
};

  
  // Show dialog to edit account
  const openEditDialog = () => {
    editUser.value = { ...user.value }; // Copy current user data to editUser
    editDialog.value = true;
  };
  
  // Logout function
  const logout = () => {
    localStorage.removeItem('user');
    localStorage.removeItem('token');
    console.log('User logged out');
    window.location.href = '/login';
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
  