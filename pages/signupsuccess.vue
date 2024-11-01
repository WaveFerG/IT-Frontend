<template>
  <div class="flex items-center justify-center bg-gradient-to-b from-blue-200 to-blue-50  min-h-screen w-full" >
    <v-sheet
      class="mb-80 p-8 max-w-md text-center"
      elevation="5"
      style="background-color: #ffffff; border-radius: 20px;"
    >
      <h1 class="text-3xl font-bold text-green-600 mb-4">ลงทะเบียนสําเร็จ!</h1>
      <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="1.5"
        stroke="currentColor"
        class="w-16 h-16 mx-auto mb-4 text-green-600"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M9 12l2 2 4-4M5.25 12a7.5 7.5 0 1 1 13.5 0 7.5 7.5 0 0 1-13.5 0z"
        />
      </svg>
      <p class="text-gray-700 mb-6">
        บัญชีของคุณถูกสร้างขึ้นเรียบร้อยแล้ว ตอนนี้คุณสามารถลงชื่อเข้าใช้บัญชีของคุณได้แล้ว
      </p>
      <p class="text-gray-500 mb-4">
        คุณจะถูกนำไปที่หน้า login ภายใน {{ countdown }} วินาที
      </p>
      <v-btn color="primary" class="w-full py-3" href="/login">
        <span class="font-bold">ไปยังหน้า login</span>
      </v-btn>
    </v-sheet>
  </div>
</template>

<script>
export default {
  data() {
    return {
      countdown: 5, // Initial countdown value
    };
  },
  mounted() {
    this.startCountdown();
  },
  methods: {
    startCountdown() {
      const countdownInterval = setInterval(() => {
        if (this.countdown > 1) {
          this.countdown--;
        } else {
          clearInterval(countdownInterval);
          this.$router.push("/login"); // Redirect to the login page
        }
      }, 1000);
    },
  },
};
</script>

<style scoped>
/* Add any additional styling here if needed */
</style>
