<script setup>
import TaskItem from './TaskItem.vue';
import { CalendarIcon, ClockIcon, TrashIcon, PencilIcon, CheckCircleIcon, PlayIcon, ArrowUturnLeftIcon } from '@heroicons/vue/24/outline';
import { CheckCircleIcon as CheckCircleIconSolid } from '@heroicons/vue/24/solid';

const props = defineProps({
  tasks: {
    type: Array,
    required: true
  },
  viewMode: {
    type: String,
    default: 'grid'
  }
});

defineEmits(['delete', 'edit', 'update-status']);

const formatDate = (dateString) => {
  if (!dateString) return '';
  const date = new Date(dateString);
  return date.toLocaleDateString('th-TH', { month: 'short', day: 'numeric', year: 'numeric' });
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
</script>

<template>
  <div>
    <!-- Grid View -->
    <div v-if="viewMode === 'grid'" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      <TaskItem 
        v-for="task in tasks" 
        :key="task.id" 
        :task="task" 
        @delete="$emit('delete', $event)"
        @edit="$emit('edit', $event)"
        @update-status="(t, s) => $emit('update-status', t, s)"
      />
    </div>

    <!-- List View -->
    <div v-else class="bg-white rounded-lg shadow overflow-hidden">
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200">
          <thead class="bg-gray-50">
            <tr>
              <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">งาน</th>
              <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">สถานะ</th>
              <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">วันที่</th>
              <th scope="col" class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">จัดการ</th>
            </tr>
          </thead>
          <tbody class="bg-white divide-y divide-gray-200">
            <tr v-for="task in tasks" :key="task.id" class="hover:bg-gray-50">
              <td class="px-6 py-4">
                <div class="flex items-center">
                  <div v-if="task.image" class="flex-shrink-0 h-10 w-10">
                    <img :src="task.image" class="h-10 w-10 rounded-full object-cover" alt="" />
                  </div>
                  <div v-else class="flex-shrink-0 h-10 w-10 bg-gray-100 rounded-full flex items-center justify-center text-gray-400">
                    <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z" />
                    </svg>
                  </div>
                  <div class="ml-4">
                    <div class="text-sm font-medium text-gray-900 line-clamp-1">{{ task.title }}</div>
                    <div class="text-sm text-gray-500 line-clamp-1">{{ task.detail }}</div>
                    <div class="flex gap-1 mt-1">
                      <span v-for="tag in task.tags" :key="tag" class="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium bg-blue-50 text-blue-800">
                        #{{ tag }}
                      </span>
                    </div>
                  </div>
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span :class="['px-2 inline-flex text-xs leading-5 font-semibold rounded-full', getStatusColor(task.status)]">
                  {{ getStatusText(task.status) }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                <div class="flex flex-col gap-1">
                  <div class="flex items-center gap-1">
                    <CalendarIcon class="w-4 h-4" />
                    <span>{{ formatDate(task.startDate) }}</span>
                  </div>
                  <div class="flex items-center gap-1">
                    <ClockIcon class="w-4 h-4" />
                    <span>{{ formatDate(task.endDate) }}</span>
                  </div>
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                <div class="flex justify-end gap-2">
                  <button v-if="task.status === 'pending'" @click="$emit('update-status', task, 'in-progress')" class="text-blue-600 hover:text-blue-900" title="เริ่มทำ">
                    <PlayIcon class="w-5 h-5" />
                  </button>
                  <button v-if="task.status === 'in-progress'" @click="$emit('update-status', task, 'completed')" class="text-green-600 hover:text-green-900" title="เสร็จสิ้น">
                    <CheckCircleIcon class="w-5 h-5" />
                  </button>
                  <button v-if="task.status === 'completed'" @click="$emit('update-status', task, 'in-progress')" class="text-yellow-600 hover:text-yellow-900" title="ทำต่อ">
                    <ArrowUturnLeftIcon class="w-5 h-5" />
                  </button>
                  <button @click="$emit('edit', task)" class="text-indigo-600 hover:text-indigo-900" title="แก้ไข">
                    <PencilIcon class="w-5 h-5" />
                  </button>
                  <button @click="$emit('delete', task.id)" class="text-red-600 hover:text-red-900" title="ลบ">
                    <TrashIcon class="w-5 h-5" />
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <div v-if="tasks.length === 0" class="text-center py-12 text-gray-500">
      ไม่พบงานในรายการ
    </div>
  </div>
</template>
