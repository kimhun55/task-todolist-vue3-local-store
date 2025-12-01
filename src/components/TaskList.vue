<script setup>
import TaskItem from './TaskItem.vue';

defineProps({
  tasks: {
    type: Array,
    required: true
  }
});

defineEmits(['delete', 'edit', 'update-status']);
</script>

<template>
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
    <TaskItem 
      v-for="task in tasks" 
      :key="task.id" 
      :task="task" 
      @delete="$emit('delete', $event)"
      @edit="$emit('edit', $event)"
      @update-status="(t, s) => $emit('update-status', t, s)"
    />
  </div>
  <div v-if="tasks.length === 0" class="text-center py-12 text-gray-500">
    ไม่พบงานในรายการ
  </div>
</template>
