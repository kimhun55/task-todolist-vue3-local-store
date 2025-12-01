<script setup>
import { ref, computed, onMounted, watch } from 'vue';
import TaskList from './components/TaskList.vue';
import TaskForm from './components/TaskForm.vue';
import TaskDetail from './components/TaskDetail.vue';
import Login from './components/Login.vue';
import { PlusIcon, Squares2X2Icon, ListBulletIcon } from '@heroicons/vue/24/outline';

const tasks = ref([]);
const isModalOpen = ref(false);
const isDetailOpen = ref(false);
const editingTask = ref(null);
const viewingTask = ref(null);
const filter = ref('all');
const viewMode = ref('grid'); // 'all', 'in-progress', 'completed'
const isAuthenticated = ref(false);

onMounted(() => {
  const savedTasks = localStorage.getItem('todo-app-data');
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
  
  const authState = localStorage.getItem('todo-app-auth');
  if (authState === 'true') {
    isAuthenticated.value = true;
  }
});

watch(tasks, (newTasks) => {
  localStorage.setItem('todo-app-data', JSON.stringify(newTasks));
}, { deep: true });

const filteredTasks = computed(() => {
  if (filter.value === 'all') return tasks.value;
  return tasks.value.filter(task => task.status === filter.value);
});

const handleLoginSuccess = () => {
  isAuthenticated.value = true;
  localStorage.setItem('todo-app-auth', 'true');
};

const logout = () => {
  isAuthenticated.value = false;
  localStorage.removeItem('todo-app-auth');
};

const openDetail = (task) => {
  console.log('openDetail called with:', task);
  viewingTask.value = task;
  isDetailOpen.value = true;
};

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
    <Login v-if="!isAuthenticated" @login-success="handleLoginSuccess" />
    
    <div v-else>
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
          <div class="flex items-center gap-4">
            <button @click="openModal()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg shadow-md transition-colors flex items-center gap-2 font-medium">
              <PlusIcon class="w-5 h-5" />
              <span class="hidden sm:inline">เพิ่มงานใหม่</span>
            </button>
            <button @click="logout" class="text-gray-500 hover:text-red-600 font-medium text-sm">
              ออกจากระบบ
            </button>
          </div>
        </div>
      </header>

      <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6 gap-4">
          <h2 class="text-xl font-semibold text-gray-800">งานของฉัน</h2>
          
          <div class="flex flex-col sm:flex-row items-stretch sm:items-center gap-4 w-full md:w-auto">
            <!-- View Toggle -->
            <div class="flex items-center justify-center bg-white p-1 rounded-lg shadow-sm border border-gray-200">
              <button 
                @click="viewMode = 'grid'" 
                :class="['p-1.5 rounded-md transition-colors flex-1 sm:flex-none justify-center flex', viewMode === 'grid' ? 'bg-blue-100 text-blue-700' : 'text-gray-500 hover:bg-gray-50']"
                title="มุมมองการ์ด"
              >
                <Squares2X2Icon class="w-5 h-5" />
              </button>
              <button 
                @click="viewMode = 'list'" 
                :class="['p-1.5 rounded-md transition-colors flex-1 sm:flex-none justify-center flex', viewMode === 'list' ? 'bg-blue-100 text-blue-700' : 'text-gray-500 hover:bg-gray-50']"
                title="มุมมองรายการ"
              >
                <ListBulletIcon class="w-5 h-5" />
              </button>
            </div>

            <!-- Filter -->
            <div class="flex items-center gap-2 bg-white p-1 rounded-lg shadow-sm border border-gray-200 overflow-x-auto scrollbar-hide">
              <button 
                @click="filter = 'all'" 
                :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap flex-1 text-center', filter === 'all' ? 'bg-blue-100 text-blue-700' : 'text-gray-600 hover:bg-gray-50']"
              >
                ทั้งหมด
              </button>
              <button 
                @click="filter = 'pending'" 
                :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap flex-1 text-center', filter === 'pending' ? 'bg-gray-100 text-gray-700' : 'text-gray-600 hover:bg-gray-50']"
              >
                รอการทำงาน
              </button>
              <button 
                @click="filter = 'in-progress'" 
                :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap flex-1 text-center', filter === 'in-progress' ? 'bg-yellow-100 text-yellow-700' : 'text-gray-600 hover:bg-gray-50']"
              >
                กำลังทำ
              </button>
              <button 
                @click="filter = 'completed'" 
                :class="['px-3 py-1.5 rounded-md text-sm font-medium transition-colors whitespace-nowrap flex-1 text-center', filter === 'completed' ? 'bg-green-100 text-green-700' : 'text-gray-600 hover:bg-gray-50']"
              >
                เสร็จแล้ว
              </button>
            </div>
          </div>
        </div>

        <TaskList 
          :tasks="filteredTasks" 
          :view-mode="viewMode"
          @delete="deleteTask" 
          @edit="openModal" 
          @view="openDetail"
          @update-status="updateStatus" 
        />
      </main>

      <TaskForm 
        :isOpen="isModalOpen" 
        :task="editingTask" 
        @close="isModalOpen = false" 
        @save="saveTask" 
      />

      <TaskDetail
        :isOpen="isDetailOpen"
        :task="viewingTask"
        @close="isDetailOpen = false"
      />
    </div>
  </div>
</template>
