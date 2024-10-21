<template>
  <v-container class="flex items-center justify-center">
    <v-sheet class="p-8 w-full max-w-lg" elevation="5" style="background-color: #eeeeee; border-radius: 20px;">
      <div class="flex items-center mb-6">
        <img src="/image/wfg.jpg" class="w-24 h-24 rounded-full" alt="Logo">
        <h1 class="text-blue-800 font-bold text-5xl my-2 mx-3 flex items-center">Sign Up</h1>
      </div>

      <h1 class="text-xl my-5 mx-5 flex items-center">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-8 h-8 mr-2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M15 19.128a9.38 9.38 0 0 0 2.625.372 9.337 9.337 0 0 0 4.121-.952 4.125 4.125 0 0 0-7.533-2.493M15 19.128v-.003c0-1.113-.285-2.16-.786-3.07M15 19.128v.106A12.318 12.318 0 0 1 8.624 21c-2.331 0-4.512-.645-6.374-1.766l-.001-.109a6.375 6.375 0 0 1 11.964-3.07M12 6.375a3.375 3.375 0 1 1-6.75 0 3.375 3.375 0 0 1 6.75 0Zm8.25 2.25a2.625 2.625 0 1 1-5.25 0 2.625 2.625 0 0 1 5.25 0Z" />
        </svg>
        Create your account
      </h1>

      <v-form @submit.prevent="submitSignUp">
        <p v-if="errorMessages.username" class="text-red-600 mx-5">{{ errorMessages.username }}</p>
        <v-text-field v-model="user.username" label="Username" variant="outlined" outlined></v-text-field>
       
        <p v-if="errorMessages.firstname" class="text-red-600 mx-5">{{ errorMessages.firstname }}</p>
        <v-text-field v-model="user.firstname" label="First Name" variant="outlined" outlined></v-text-field>

        <p v-if="errorMessages.lastname" class="text-red-600 mx-5">{{ errorMessages.lastname }}</p>
        <v-text-field v-model="user.lastname" label="Last Name" variant="outlined" outlined></v-text-field>
       
        <p v-if="errorMessages.email" class="text-red-600 mx-5">{{ errorMessages.email }}</p>
        <v-text-field v-model="user.email" label="Email" variant="outlined" outlined></v-text-field>
        
        <p v-if="errorMessages.phone_number" class="text-red-600 mx-5">{{ errorMessages.phone_number }}</p>
        <v-text-field v-model="user.phone_number" label="Phone Number" variant="outlined" outlined></v-text-field>
        
        <p v-if="errorMessages.department" class="text-red-600 mx-5">{{ errorMessages.department }}</p>
        <v-text-field v-model="user.department" label="Department" variant="outlined" outlined></v-text-field>
        
        <p v-if="errorMessages.password" class="text-red-600 mx-5">{{ errorMessages.password }}</p>
        <v-text-field v-model="user.password" label="Password" type="password" variant="outlined" outlined></v-text-field>
        
        <p v-if="!agreeToPolicy && showValidationMessage" class="text-red-600 mx-5">You must agree to the services and privacy policy before signing up.</p>
        <v-checkbox v-model="agreeToPolicy" label="I agree to the services and privacy policy"></v-checkbox>
        

        <v-btn 
          type="submit" 
          color="primary" 
          class="w-full m-2" 
          :disabled="!agreeToPolicy">
          <span class="font-bold">Sign Up</span>
        </v-btn>

        <div class="flex items-center mb-6">
          <h1 class="mx-3 text-base">Already a member? </h1>
          <a class="text-blue-800" href="/login"> Log in </a>
        </div>
      </v-form>
    </v-sheet>
  </v-container>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
import Swal from 'sweetalert2';

const user = ref({
  username: '',
  firstname: '',
  lastname: '',
  email: '',
  phone_number: '',
  department: '',
  password: ''
});

const errorMessages = ref({
  username: '',
  firstname: '',
  lastname: '',
  email: '',
  password: '',
  department: '',
  phone_number: '',
});

const agreeToPolicy = ref(false);
const showValidationMessage = ref(true);
const router = useRouter();

const submitSignUp = async () => {
  if (!agreeToPolicy.value) { 
    showValidationMessage.value = true; 
    return;
  }

  // Clear previous error messages
  Object.keys(errorMessages.value).forEach(key => {
    errorMessages.value[key] = '';
  });

  try {
    const response = await axios.post('http://localhost:8000/users/postusers', user.value);
    console.log('Sign up successful:', response.data);
    router.push('/signupsuccess'); // Redirect to signupsuccess page
  } catch (error) {
    console.error('Sign up error:', error);

    if (error.response && error.response.data.errors) {
      const validationErrors = error.response.data.errors;

      // Map the validation errors to the errorMessages object
      validationErrors.forEach(err => {
        if (err.path && errorMessages.value[err.path] !== undefined) {
          errorMessages.value[err.path] = err.msg; // Set the message for the corresponding field
        }
      });
    } else {
      // Handle case where there is no response
      console.error('Network error or no response from server');
    }
  }

  showValidationMessage.value = false; 
};
</script>
