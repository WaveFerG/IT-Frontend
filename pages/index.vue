<template>
  <div class="min-h-screen bg-gradient-to-b from-blue-200 to-blue-50 flex flex-col items-center justify-center p-6 space-y-8">
    
    <div class="flex items-center space-x-4">
      <img src="/icon.jpg" alt="App Icon" class="w-20 h-20 rounded-full shadow-lg">
      <h1 class="text-4xl font-extrabold text-gray-800">
        Welcome to Conference Room Booking
      </h1>
    </div>
    
    <p class="text-lg text-gray-600 max-w-2xl text-center">
      เว็บที่มีเนื้อหาเกี่ยวกับการจองห้องประชุมที่มีฟิลเตอร์ให้เลือกมากมาย ด้วยระบบจองห้องประชุมที่สะดวกและรวดเร็ว ค้นหาและจองห้องประชุมที่ตอบโจทย์คุณได้ทันที 
    </p>
    <p class="text-lg text-gray-600 max-w-xl text-center">
      หากท่านเข้าทางโทรศัพท์เพื่อความสะดวกแนะนำให้ทำการจองผ่าน Application Conference Room Booking
    </p>

    <a href="/home" class="block">
      <button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-lg shadow-lg transform hover:scale-105 transition duration-200 ease-in-out">
        เริ่มต้นด้วยหน้า Home
      </button>
    </a>
    
    <a href="/login" class="block">
      <button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-6 rounded-lg shadow-lg transform hover:scale-105 transition duration-200 ease-in-out">
        ไปยังหน้า Login
      </button>
    </a>

    <!-- Image Grid Section -->
    <div class="grid grid-cols-2 gap-4 w-full md:w-2/3 lg:w-1/2">
      <img class="rounded-lg shadow-lg object-cover h-48 w-full" src="/index1.jpg" alt="Conference Room 1">
      <img class="rounded-lg shadow-lg object-cover h-48 w-full" src="/index3.jpg" alt="Conference Room 2">
      <img class="rounded-lg shadow-lg object-cover h-48 w-full" src="/index2.jpg" alt="Conference Room 3">
      <img class="rounded-lg shadow-lg object-cover h-48 w-full" src="/index4.jpg" alt="Conference Room 4">
    </div>
  </div>
</template>
