<script setup lang="ts">
  import { defineProps } from 'vue'

  const props = defineProps({
    task: {
      type: Object,
      required: true,
      default: () => ({
        name: '',
        description: '',
        deadline: null,
        priority: false
      })
    }
  })

  const emit = defineEmits(['edit'])

  const editTask = () => {
    emit('edit', props.task)
  }

  const formatDate = (dateString) => {
    const options = { year: 'numeric', month: 'short', day: 'numeric' }
    return new Date(dateString).toLocaleDateString('ru-RU', options)
  }
</script>

<template>
  <div>
    <div class="task-card">
      <div class="task-header">
        <h3 class="task-title">{{ task.name }}</h3>
        <button class="edit-btn" @click="editTask">
          <img src="./icons/edit.svg" alt="Редактировать" />
        </button>
      </div>

      <p class="task-description" v-if="task.description">{{ task.description }}</p>

      <div class="task-footer">
        <span class="task-date" v-if="task.deadline">
          📅 {{ formatDate(task.deadline) }}
        </span>
        <span class="task-priority" v-if="task.priority">
          ⚠️ Приоритет
        </span>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .task-card {
    background: white;
    border-radius: 8px;
    padding: 12px;
    margin-bottom: 12px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    transition: transform 0.2s, box-shadow 0.2s;
  }

    .task-card:hover {
      transform: translateY(-2px);
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
    }

  .task-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 8px;
  }

  .task-title {
    margin: 0;
    font-size: 16px;
    font-weight: 600;
    color: #333;
    flex-grow: 1;
  }

  .edit-btn {
    background: none;
    border: none;
    cursor: pointer;
    padding: 4px;
    margin-left: 8px;
  }

    .edit-btn img {
      width: 16px;
      height: 16px;
      opacity: 0.6;
      transition: opacity 0.2s;
    }

    .edit-btn:hover img {
      opacity: 1;
    }

  .task-description {
    margin: 8px 0;
    font-size: 14px;
    color: #666;
    line-height: 1.4;
  }

  .task-footer {
    display: flex;
    justify-content: space-between;
    margin-top: 8px;
    font-size: 12px;
    color: #888;
  }

  .task-date, .task-priority {
    display: flex;
    align-items: center;
    gap: 4px;
  }
</style>
