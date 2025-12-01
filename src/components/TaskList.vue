<script setup>
import TaskItem from './TaskItem.vue';

defineProps({
  tasks: {
    type: Array,
    required: true
  }
});

defineEmits(['delete', 'edit', 'toggle-status']);
</script>

<template>
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
    <TaskItem 
      v-for="task in tasks" 
      :key="task.id" 
      :task="task" 
      @delete="$emit('delete', $event)"
      @edit="$emit('edit', $event)"
      @toggle-status="$emit('toggle-status', $event)"
    />
  </div>
  <div v-if="tasks.length === 0" class="text-center py-12 text-gray-500">
    ไม่พบงานในรายการ
  </div>
</template>
