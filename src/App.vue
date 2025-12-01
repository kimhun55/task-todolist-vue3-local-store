<script setup>
import { ref, computed, onMounted, watch } from 'vue';
import TaskList from './components/TaskList.vue';
import TaskForm from './components/TaskForm.vue';
import { PlusIcon } from '@heroicons/vue/24/outline';

const tasks = ref([]);
const isModalOpen = ref(false);
const editingTask = ref(null);
const filter = ref('all'); // 'all', 'in-progress', 'completed'

onMounted(() => {
  const savedTasks = localStorage.getItem('todo-app-data');
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
});

watch(tasks, (newTasks) => {
  localStorage.setItem('todo-app-data', JSON.stringify(newTasks));
}, { deep: true });

const filteredTasks = computed(() => {
  if (filter.value === 'all') return tasks.value;
  return tasks.value.filter(task => task.status === filter.value);
});

const openModal = (task = null) => {
  editingTask.value = task;
  isModalOpen.value = true;
};

const closeModal = () => {
  editingTask.value = null;
  isModalOpen.value = false;
};

const saveTask = (taskData) => {
  if (editingTask.value) {
    // Update existing
    const index = tasks.value.findIndex(t => t.id === editingTask.value.id);
    if (index !== -1) {
      tasks.value[index] = { ...taskData, id: editingTask.value.id, status: editingTask.value.status };
    }
  } else {
    // Add new
    const newId = typeof crypto !== 'undefined' && crypto.randomUUID ? crypto.randomUUID() : Date.now().toString();
    console.log('Saving new task:', taskData, newId);
    tasks.value.push({
      ...taskData,
      id: newId,
      status: 'pending',
      createdAt: new Date().toISOString()
    });
  }
  closeModal();
};

const deleteTask = (id) => {
  // Removed confirm dialog to simplify interaction for now
  tasks.value = tasks.value.filter(t => t.id !== id);
};

const updateStatus = (task, newStatus) => {
  const index = tasks.value.findIndex(t => t.id === task.id);
  if (index !== -1) {
    tasks.value[index].status = newStatus;
  }
};
</script>

<template>
  <div class="min-h-screen bg-gray-50 font-sans text-gray-900">
    <header class="bg-white shadow-sm sticky top-0 z-10">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex justify-between items-center">
        <div class="flex items-center gap-3">
          <div class="bg-blue-600 text-white p-2 rounded-lg">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </div>
          <h1 class="text-2xl font-bold text-gray-900 tracking-tight">รายการสิ่งที่ต้องทำ</h1>
        </div>
        <button @click="openModal()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg shadow-md transition-colors flex items-center gap-2 font-medium">
          <PlusIcon class="w-5 h-5" />
          เพิ่มงานใหม่
        </button>
      </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <div class="flex justify-between items-center mb-6">
        <h2 class="text-xl font-semibold text-gray-800">งานของฉัน</h2>
        <div class="flex items-center gap-2 bg-white p-1 rounded-lg shadow-sm border border-gray-200 overflow-x-auto">
          <button 
            @click="filter = 'all'" 
            :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap', filter === 'all' ? 'bg-blue-100 text-blue-700' : 'text-gray-600 hover:bg-gray-50']"
          >
            ทั้งหมด
          </button>
          <button 
            @click="filter = 'pending'" 
            :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap', filter === 'pending' ? 'bg-gray-100 text-gray-700' : 'text-gray-600 hover:bg-gray-50']"
          >
            รอการทำงาน
          </button>
          <button 
            @click="filter = 'in-progress'" 
            :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap', filter === 'in-progress' ? 'bg-yellow-100 text-yellow-700' : 'text-gray-600 hover:bg-gray-50']"
          >
            กำลังทำ
          </button>
          <button 
            @click="filter = 'completed'" 
            :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap', filter === 'completed' ? 'bg-green-100 text-green-700' : 'text-gray-600 hover:bg-gray-50']"
          >
            เสร็จแล้ว
          </button>
        </div>
      </div>

      <TaskList 
        :tasks="filteredTasks" 
        @delete="deleteTask" 
        @edit="openModal" 
        @update-status="updateStatus" 
      />
    </main>

    <TaskForm 
      :isOpen="isModalOpen" 
      :task="editingTask" 
      @close="closeModal" 
      @save="saveTask" 
    />
  </div>
</template>
