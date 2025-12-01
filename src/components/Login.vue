<script setup>
import { ref } from 'vue';

const username = ref('');
const password = ref('');
const error = ref('');

const emit = defineEmits(['login-success']);

const handleLogin = () => {
  const validUsername = import.meta.env.VITE_USERNAME;
  const validPassword = import.meta.env.VITE_PASSWORD;

  if (username.value === validUsername && password.value === validPassword) {
    emit('login-success');
  } else {
    error.value = 'ชื่อผู้ใช้หรือรหัสผ่านไม่ถูกต้อง';
  }
};
</script>

<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-md w-full space-y-8 bg-white p-8 rounded-lg shadow-md">
      <div>
        <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
          เข้าสู่ระบบ
        </h2>
        <p class="mt-2 text-center text-sm text-gray-600">
          ระบบจัดการงาน (To-Do List)
        </p>
      </div>
      <form class="mt-8 space-y-6" @submit.prevent="handleLogin">
        <div class="rounded-md shadow-sm -space-y-px">
          <div>
            <label for="username" class="sr-only">ชื่อผู้ใช้</label>
            <input id="username" name="username" type="text" required v-model="username" class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-blue-500 focus:border-blue-500 focus:z-10 sm:text-sm" placeholder="ชื่อผู้ใช้">
          </div>
          <div>
            <label for="password" class="sr-only">รหัสผ่าน</label>
            <input id="password" name="password" type="password" required v-model="password" class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-b-md focus:outline-none focus:ring-blue-500 focus:border-blue-500 focus:z-10 sm:text-sm" placeholder="รหัสผ่าน">
          </div>
        </div>

        <div v-if="error" class="text-red-500 text-sm text-center">
          {{ error }}
        </div>

        <div>
          <button type="submit" class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
            เข้าสู่ระบบ
          </button>
        </div>
      </form>
    </div>
  </div>
</template>
