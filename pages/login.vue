<template>
  <div class="flex items-center justify-center bg-gradient-to-b from-blue-200 to-blue-50 min-h-screen w-full">
    <v-sheet class="mb-60 p-8 w-full max-w-lg " elevation="5" style="background-color: #ffffff; border-radius: 20px;">
      <div class="flex items-center mb-6 ">
        <img src="/icon.jpg" class="w-36 h-36 rounded-full  " alt="Logo">
        <h1 class="text-blue-800 font-bold text-5xl my-2 mx-1 flex items-center">Login</h1>
      </div>

      <v-form @submit.prevent="submitLogin">
        <v-text-field v-model="login.username" label="ชื่อผู้ใช้" variant="outlined" outlined></v-text-field>
        <v-text-field v-model="login.password" label="รหัสผ่าน" type="password" variant="outlined" outlined></v-text-field>
        <v-btn type="submit" color="primary" class="w-full m-2">
          <span class="font-bold">Login</span>
        </v-btn>
        <div v-if="errorMessage" class="text-red-500 mb-4">
          {{ errorMessage }}
        </div>
        <div class="flex items-center mb-2">
          <h1 class="mx-3 text-base">ไม่มีบัญชีผู้ใช้สมัครได้ที่นี่</h1>
          <a class="text-blue-800" href="/signup"> Sign up </a>
        </div>
        <a class="mx-3 text-blue-500" href="#" @click.prevent="openForgotPasswordDialog">หากคุณลืมรหัสผ่าน? </a>
      </v-form>

      <!-- Forgot Password Dialog -->
      <v-dialog v-model="forgotPasswordDialog" max-width="400px">
        <v-card>
          <v-card-title>
            <span class="headline">ลืมรหัสผ่าน</span>
          </v-card-title>
          <span class="ml-10">โปรดกรอก อีเมล ที่ท่านใช้สมัคร</span>
          
          <v-card-text>
            <v-text-field v-model="forgotPasswordEmail" label="อีเมล" type="email" required></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn text @click="forgotPasswordDialog = false">ยกเลิก</v-btn>
            <v-btn text color="primary" @click="submitForgotPassword">ยืนยัน</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- Confirmation Code Dialog -->
      <v-dialog v-model="confirmationCodeDialog" max-width="400px">
        <v-card>
          <v-card-title>
            <span class="headline">ใส่โค้ดยืนยันลืมรหัสผ่าน</span>
          </v-card-title>
          <span class="ml-10">โปรดกรอกโค้ดที่ท่านได้รับทาง อีเมล</span>
          <v-card-text>
            <v-text-field v-model="confirmationCode" label="โค้ดยืนยัน" required></v-text-field>
            <div v-if="timeLeft > 0" class="mt-2">
              <p>Time left: {{ formatTime(timeLeft) }}</p>
            </div>
            <div v-else class="mt-2 text-red-500">
              <p>เวลาหมดแล้ว โปรดขอโค้ดยืนยันรหัสผ่านใหม่</p>
            </div>
          </v-card-text>
          <v-card-actions>
            <v-btn text @click="cancelDialog">Cancel</v-btn>
            <v-btn text color="primary" @click="submitConfirmationCode" :disabled="timeLeft <= 0">Submit</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- Reset Password Dialog -->
      <v-dialog v-model="resetPasswordDialog" max-width="400px">
        <v-card>
          <v-card-title>
            <span class="headline">รีเซ็ท รหัสผ่าน</span>
          </v-card-title>
          <v-card-text>
            <v-text-field v-model="newPassword" label="รหัสผ่านใหม่" type="password" required></v-text-field>
            <v-text-field v-model="repeatPassword" label="ใส่รหัสผ่านใหม่อีกครั้ง" type="password" required></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn text @click="resetPasswordDialog = false">Cancel</v-btn>
            <v-btn text color="primary" @click="submitResetPassword">Submit</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- Snackbar for Notifications -->
      <v-snackbar v-model="snackbar.visible" :color="snackbar.color" timeout="3000">
        {{ snackbar.message }}
        <template v-slot:action>
          <v-btn color="white" text @click="snackbar.visible = false">Close</v-btn>
        </template>
      </v-snackbar>
    </v-sheet>
  </div>
</template>

<script setup>
import { ref , watch} from 'vue';
import axios from 'axios';
import Swal from 'sweetalert2';

const login = ref({
  username: '',
  password: ''
});

const errorMessage = ref('');
const forgotPasswordDialog = ref(false);
const confirmationCodeDialog = ref(false);
const resetPasswordDialog = ref(false);
const forgotPasswordEmail = ref('');
const confirmationCode = ref('');
const newPassword = ref('');
const repeatPassword = ref('');
const timeLeft = ref(300); 
let timer = null;





// Snackbar state
const snackbar = ref({
  visible: false,
  message: '',
  color: 'success' // Default to success, change on error
});

// Function to handle login
const submitLogin = async () => {
  try {
    const response = await axios.post('http://localhost:8000/users/login', {
      username: login.value.username,
      password: login.value.password
    });
    console.log('API response:', response.data);

    if (response.data.status === "success" && response.data.code === 1) {
      localStorage.setItem('token', response.data.token);
      localStorage.setItem('user', JSON.stringify(response.data.result));
      console.log('Logged in with Token:', response.data.token);
      console.log('Logged in with User:', response.data.result);

      Swal.fire({
        title: 'Login สำเร็จแล้ว!',
        text: 'คุณจะถูกเปลี่ยนเส้นทางในไม่ช้า.',
        icon: 'success',
        timer: 1000,
        showConfirmButton: false
      });

      setTimeout(() => {
        window.location.href = '/home';
      }, 1000);
    } else {
      const errorMessage = response.data.message || 'Invalid username or password.';
      Swal.fire({
        title: 'Error',
        text: errorMessage,
        icon: 'error',
        confirmButtonText: 'โปรดลองใหม่อีกครั้ง'
      });
    }
  } catch (error) {
    console.error('Login error:', error);
    const errorMsg = error.response?.data?.message || 'An error occurred. Please try again.';
    Swal.fire({
      title: 'Error',
      text: errorMsg,
      icon: 'error',
      confirmButtonText: 'โปรดลองใหม่อีกครั้ง'
    });
  }
};

// Function to open the forgot password dialog
const openForgotPasswordDialog = () => {
  forgotPasswordDialog.value = true; // Open the dialog
};

// Function to submit the forgot password request
const submitForgotPassword = async () => {
  try {
    const response = await axios.post('http://localhost:8000/users/forgot-password', {
      email: forgotPasswordEmail.value
    });
    snackbar.value = {
      visible: true,
      message: response.data.message,
      color: 'success'
    };
    localStorage.setItem('forgotPasswordEmail', forgotPasswordEmail.value); // Save email to localStorage
    forgotPasswordDialog.value = false; // Close the dialog
    forgotPasswordEmail.value = ''; // Clear the email input
    confirmationCodeDialog.value = true; // Open confirmation code dialog
  } catch (error) {
    console.error('Forgot password error:', error);
    snackbar.value = {
      visible: true,
      message: error.response?.data?.message || 'An error occurred. Please try again.',
      color: 'error'
    };
  }
};

// Function to submit the confirmation code
const submitConfirmationCode = async () => {
  const email = localStorage.getItem('forgotPasswordEmail'); // Retrieve email from localStorage
  try {
    const response = await axios.post('http://localhost:8000/users/verifyConfirmationCode', {
      email: email,
      confirmationCode: confirmationCode.value
    });
    snackbar.value = {
      visible: true,
      message: response.data.message,
      color: 'success'
    };
    confirmationCodeDialog.value = false; // Close the confirmation code dialog
    confirmationCode.value = ''; // Clear the confirmation code input
    resetPasswordDialog.value = true; // Open reset password dialog
  } catch (error) {
    console.error('Verification error:', error);
    snackbar.value = {
      visible: true,
      message: error.response?.data?.message || 'An error occurred. Please try again.',
      color: 'error'
    };
  }
};

const formatTime = (seconds) => {
  const minutes = Math.floor(seconds / 60);
  const secs = seconds % 60;
  return `${minutes}:${secs < 10 ? '0' : ''}${secs}`;
};

const startTimer = () => {
  timer = setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--;
    } else {
      clearInterval(timer);
      confirmationCodeDialog.value = false; // Close dialog when time expires
    }
  }, 1000);
};

const cancelDialog = () => {
  confirmationCodeDialog.value = false;
  clearInterval(timer);
};

onMounted(() => {
  if (confirmationCodeDialog.value) {
    startTimer();
  }
});

onBeforeUnmount(() => {
  clearInterval(timer);
});

watch(confirmationCodeDialog, (newValue) => {
  if (newValue) {
    startTimer(); // Start the timer when dialog opens
  } else {
    clearInterval(timer); // Clear the timer when dialog closes
    timeLeft.value = 300; // Reset the timer
  }
});


// Function to submit the reset password
const submitResetPassword = async () => {
  const email = localStorage.getItem('forgotPasswordEmail'); // Retrieve email from localStorage
  try {
    const response = await axios.post('http://localhost:8000/users/reset-password', {
      email: email,
      newPassword: newPassword.value,
      repeatPassword: repeatPassword.value
    });
    snackbar.value = {
      visible: true,
      message: response.data.message, // ใช้ข้อความจาก backend
      color: 'success'
    };
    localStorage.removeItem('forgotPasswordEmail');
    resetPasswordDialog.value = false; // Close the reset password dialog
    newPassword.value = ''; // Clear new password input
    repeatPassword.value = ''; // Clear repeat password input
  } catch (error) {
    console.error('Reset password error:', error);
    // ตรวจสอบการตอบกลับจาก backend และแสดงข้อความที่เหมาะสม
    snackbar.value = {
      visible: true,
       message: error.response.data.errors?.[0]?.msg || 'An error occurred. Please try again.',
      color: 'error'
    };
  }
};


</script>

<style scoped>
</style>
