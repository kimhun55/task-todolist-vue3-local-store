<script setup>
import { ref, watch } from 'vue';
import { XMarkIcon, PhotoIcon } from '@heroicons/vue/24/outline';

const props = defineProps({
  task: {
    type: Object,
    default: null
  },
  isOpen: {
    type: Boolean,
    required: true
  }
});

const emit = defineEmits(['close', 'save']);

const form = ref({
  title: '',
  detail: '',
  image: '',
  startDate: '',
  endDate: '',
  tags: ''
});

watch(() => props.task, (newTask) => {
  if (newTask) {
    form.value = {
      ...newTask,
      tags: newTask.tags ? newTask.tags.join(', ') : '',
      startDate: newTask.startDate ? newTask.startDate.split('T')[0] : '',
      endDate: newTask.endDate ? newTask.endDate.split('T')[0] : ''
    };
  } else {
    resetForm();
  }
}, { immediate: true });

function resetForm() {
  form.value = {
    title: '',
    detail: '',
    image: '',
    startDate: '',
    endDate: '',
    tags: ''
  };
}

const handleImageUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      form.value.image = e.target.result;
    };
    reader.readAsDataURL(file);
  }
};

const handleSubmit = () => {
  const taskData = {
    ...form.value,
    tags: typeof form.value.tags === 'string' ? form.value.tags.split(',').map(tag => tag.trim()).filter(tag => tag) : [],
    startDate: form.value.startDate ? new Date(form.value.startDate).toISOString() : null,
    endDate: form.value.endDate ? new Date(form.value.endDate).toISOString() : null
  };
  emit('save', taskData);
  resetForm();
};
</script>

<template>
  <div v-if="isOpen" class="fixed inset-0 z-50 overflow-y-auto" aria-labelledby="modal-title" role="dialog" aria-modal="true">
    <div class="flex items-center justify-center min-h-screen p-4 text-center">
      <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" aria-hidden="true" @click="$emit('close')"></div>

      <div class="relative inline-block bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all w-full max-w-lg">
        <div class="bg-white px-4 pt-5 pb-4 sm:p-6 sm:pb-4">
          <div class="flex justify-between items-center mb-4">
            <h3 class="text-lg leading-6 font-medium text-gray-900" id="modal-title">
              {{ task ? 'แก้ไขงาน' : 'สร้างงานใหม่' }}
            </h3>
            <button @click="$emit('close')" class="text-gray-400 hover:text-gray-500">
              <XMarkIcon class="w-6 h-6" />
            </button>
          </div>
          
          <form @submit.prevent="handleSubmit" class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700">หัวข้อ</label>
              <input v-model="form.title" type="text" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm border p-2" placeholder="ชื่อหัวข้องาน" />
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700">รายละเอียด</label>
              <textarea v-model="form.detail" rows="3" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm border p-2" placeholder="รายละเอียดของงาน..."></textarea>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700">รูปภาพ</label>
              <div class="mt-1 flex items-center gap-2">
                <div v-if="form.image" class="relative h-16 w-16 rounded-md overflow-hidden">
                  <img :src="form.image" class="h-full w-full object-cover" />
                  <button @click="form.image = ''" type="button" class="absolute top-0 right-0 bg-red-500 text-white rounded-full p-0.5 m-0.5">
                    <XMarkIcon class="w-3 h-3" />
                  </button>
                </div>
                <label class="cursor-pointer bg-white py-2 px-3 border border-gray-300 rounded-md shadow-sm text-sm leading-4 font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
                  <PhotoIcon class="w-5 h-5 inline-block mr-1" />
                  อัปโหลด
                  <input type="file" class="hidden" accept="image/*" @change="handleImageUpload" />
                </label>
              </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
              <div>
                <label class="block text-sm font-medium text-gray-700">วันที่เริ่ม</label>
                <input v-model="form.startDate" type="date" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm border p-2" />
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700">วันที่สิ้นสุด</label>
                <input v-model="form.endDate" type="date" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm border p-2" />
              </div>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700">แท็ก (คั่นด้วยเครื่องหมายจุลภาค)</label>
              <input v-model="form.tags" type="text" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm border p-2" placeholder="งาน, ด่วน, ออกแบบ" />
            </div>

            <div class="pt-4 flex justify-end gap-3">
              <button type="button" @click="$emit('close')" class="bg-white py-2 px-4 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
                ยกเลิก
              </button>
              <button type="submit" class="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
                บันทึก
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>
