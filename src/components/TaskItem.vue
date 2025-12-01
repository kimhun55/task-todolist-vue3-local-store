<script setup>
import { computed } from 'vue';
import { CalendarIcon, ClockIcon, TrashIcon, PencilIcon, CheckCircleIcon, PlayIcon, ArrowUturnLeftIcon, EyeIcon } from '@heroicons/vue/24/outline';
import { CheckCircleIcon as CheckCircleIconSolid } from '@heroicons/vue/24/solid';

const props = defineProps({
  task: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(['delete', 'edit', 'update-status', 'view']);

const formatDate = (dateString) => {
  if (!dateString) return '';
  const date = new Date(dateString);
  return date.toLocaleDateString('th-TH', { month: 'short', day: 'numeric', year: 'numeric' });
};

const statusColor = computed(() => {
  if (props.task.status === 'completed') return 'bg-green-100 text-green-800';
  if (props.task.status === 'in-progress') return 'bg-yellow-100 text-yellow-800';
  return 'bg-gray-100 text-gray-800';
});

const statusText = computed(() => {
  if (props.task.status === 'completed') return 'เสร็จแล้ว';
  if (props.task.status === 'in-progress') return 'กำลังทำ';
  return 'รอการทำงาน';
});
</script>

<template>
  <div class="bg-white rounded-xl shadow-md overflow-hidden hover:shadow-lg transition-shadow duration-300 border border-gray-100 flex flex-col h-full">
    <div v-if="task.image" class="h-48 w-full overflow-hidden shrink-0">
      <img :src="task.image" alt="Task Image" class="w-full h-full object-cover" />
    </div>
    <div class="p-5 flex flex-col flex-grow">
      <div class="flex justify-between items-start mb-2">
        <h3 class="text-xl font-bold text-gray-900 line-clamp-1" :class="{ 'line-through text-gray-500': task.status === 'completed' }">
          {{ task.title }}
        </h3>
        <span :class="['px-2 py-1 text-xs font-semibold rounded-full', statusColor]">
          {{ statusText }}
        </span>
      </div>
      
      <p class="text-gray-600 text-sm mb-4 line-clamp-2 flex-grow">{{ task.detail }}</p>

      <div class="flex flex-wrap gap-2 mb-4">
        <span v-for="tag in task.tags" :key="tag" class="px-2 py-1 bg-blue-50 text-blue-600 text-xs rounded-md">
          #{{ tag }}
        </span>
      </div>

      <div class="flex items-center gap-4 text-xs text-gray-500 mb-4">
        <div class="flex items-center gap-1">
          <CalendarIcon class="w-4 h-4" />
          <span>เริ่ม: {{ formatDate(task.startDate) }}</span>
        </div>
        <div class="flex items-center gap-1">
          <ClockIcon class="w-4 h-4" />
          <span>สิ้นสุด: {{ formatDate(task.endDate) }}</span>
        </div>
      </div>

      <div class="flex justify-end gap-2 pt-3 border-t border-gray-100 mt-auto">
        <button v-if="task.status === 'pending'" @click="$emit('update-status', task, 'in-progress')" class="p-2 text-gray-500 hover:text-blue-600 transition-colors rounded-full hover:bg-blue-50" title="เริ่มทำ">
          <PlayIcon class="w-5 h-5" />
        </button>
        <button v-if="task.status === 'in-progress'" @click="$emit('update-status', task, 'completed')" class="p-2 text-gray-500 hover:text-green-600 transition-colors rounded-full hover:bg-green-50" title="เสร็จสิ้น">
          <CheckCircleIcon class="w-5 h-5" />
        </button>
        <button v-if="task.status === 'completed'" @click="$emit('update-status', task, 'in-progress')" class="p-2 text-gray-500 hover:text-yellow-600 transition-colors rounded-full hover:bg-yellow-50" title="ทำต่อ">
          <ArrowUturnLeftIcon class="w-5 h-5" />
        </button>
        
        <button @click="$emit('view', task)" class="p-2 text-gray-500 hover:text-indigo-600 transition-colors rounded-full hover:bg-indigo-50" title="ดูรายละเอียด">
          <EyeIcon class="w-5 h-5" />
        </button>
        <button @click="$emit('edit', task)" class="p-2 text-gray-500 hover:text-blue-600 transition-colors rounded-full hover:bg-blue-50" title="แก้ไข">
          <PencilIcon class="w-5 h-5" />
        </button>
        <button @click="$emit('delete', task.id)" class="p-2 text-gray-500 hover:text-red-600 transition-colors rounded-full hover:bg-red-50" title="ลบ">
          <TrashIcon class="w-5 h-5" />
        </button>
      </div>
    </div>
  </div>
</template>
