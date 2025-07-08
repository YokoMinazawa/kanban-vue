
<script setup lang="ts">
  import FieldCard from './FieldCard.vue'
  import { ref } from 'vue'
  const columns = [
    { title: 'Пулл'},
    { title: 'В работе'},
    { title: 'Тест'},
    { title: 'Выполнено'}
  ]
</script>

<template>
  <div class="column-container">
    <div v-for="(column, index) in columns"
         :key="index"
         class="column decoration"
         :data-title="column.title">
    </div>
    <FieldCard/>
  </div>
</template>

<style lang="css" scoped>
  .column-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 30px;
    padding: 20px;
    overflow-y: auto;
  }

  .column {
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
    border-radius: 0 10px 10px 10px;
    padding: 15px;
    height: 9000px;
    width: 200px;
  }

  .decoration::before {
    display: flex;
    content: attr(data-title);
    transform: translateX(-50%);
    position: absolute;
    margin-left: 35px;
    top: 0;
    transform: translateX(-50%);
    width: 100px;
    height: 20px;
    border-radius: 10px 10px 0 0;
    box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.2);
    place-content: center;
  }

  .content {
    margin-top: 10px;
  }
</style>
