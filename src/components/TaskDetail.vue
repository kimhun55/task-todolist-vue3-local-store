<script setup>
import { XMarkIcon, CalendarIcon, ClockIcon } from '@heroicons/vue/24/outline';

const props = defineProps({
  isOpen: Boolean,
  task: Object
});

const emit = defineEmits(['close']);

const formatDate = (dateString) => {
  if (!dateString) return '';
  const date = new Date(dateString);
  return date.toLocaleDateString('th-TH', { 
    weekday: 'long', 
    year: 'numeric', 
    month: 'long', 
    day: 'numeric' 
  });
};

const getStatusColor = (status) => {
  if (status === 'completed') return 'bg-green-100 text-green-800';
  if (status === 'in-progress') return 'bg-yellow-100 text-yellow-800';
  return 'bg-gray-100 text-gray-800';
};

const getStatusText = (status) => {
  if (status === 'completed') return 'เสร็จแล้ว';
  if (status === 'in-progress') return 'กำลังทำ';
  return 'รอการทำงาน';
};

import { onMounted } from 'vue';
onMounted(() => {
  console.log('TaskDetail component mounted');
});
</script>

<template>
  <div v-if="isOpen" class="fixed inset-0 z-50 overflow-y-auto" aria-labelledby="modal-title" role="dialog" aria-modal="true">
    <div class="flex items-center justify-center min-h-screen p-4 text-center">
      <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" aria-hidden="true" @click="$emit('close')"></div>

      <div class="relative inline-block bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all w-full max-w-2xl">
        <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
          <div class="flex justify-between items-start mb-6">
            <h3 class="text-2xl leading-6 font-bold text-gray-900" id="modal-title">
              {{ task?.title }}
            </h3>
            <button @click="$emit('close')" class="text-gray-400 hover:text-gray-500">
              <XMarkIcon class="w-6 h-6" />
            </button>
          </div>

          <div class="space-y-6">
            <!-- Image -->
            <div v-if="task?.image" class="w-full h-64 rounded-lg overflow-hidden bg-gray-100">
              <img :src="task.image" class="w-full h-full object-contain" alt="Task Image" />
            </div>

            <!-- Status & Tags -->
            <div class="flex flex-wrap items-center gap-3">
              <span :class="['px-3 py-1 text-sm font-semibold rounded-full', getStatusColor(task?.status)]">
                {{ getStatusText(task?.status) }}
              </span>
              <span v-for="tag in task?.tags" :key="tag" class="px-2 py-1 bg-blue-50 text-blue-600 text-sm rounded-md">
                #{{ tag }}
              </span>
            </div>

            <!-- Detail -->
            <div>
              <h4 class="text-sm font-medium text-gray-500 mb-2">รายละเอียด</h4>
              <p class="text-gray-700 whitespace-pre-wrap">{{ task?.detail || '-' }}</p>
            </div>

            <!-- Dates -->
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 bg-gray-50 p-4 rounded-lg">
              <div class="flex items-center gap-2 text-gray-600">
                <CalendarIcon class="w-5 h-5 text-blue-500" />
                <div>
                  <p class="text-xs text-gray-500">วันที่เริ่ม</p>
                  <p class="font-medium">{{ formatDate(task?.startDate) || '-' }}</p>
                </div>
              </div>
              <div class="flex items-center gap-2 text-gray-600">
                <ClockIcon class="w-5 h-5 text-red-500" />
                <div>
                  <p class="text-xs text-gray-500">วันที่สิ้นสุด</p>
                  <p class="font-medium">{{ formatDate(task?.endDate) || '-' }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
        
        <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
          <button type="button" @click="$emit('close')" class="w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 sm:ml-3 sm:w-auto sm:text-sm">
            ปิด
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
