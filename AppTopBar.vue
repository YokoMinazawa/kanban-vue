<script setup lang="ts">
  import { ref } from 'vue'
  import TopBarTaskCreate from './TopBarTaskCreate.vue'
  import TopBarAccountSettings from './TopBarAccountSettings.vue'

  const projects = ref<Array<{ id: number, name: string }>>([])
  const currentProject = ref<number | null>(null)
  const newProjectName = ref('')
  const showProjectDropdown = ref(false)

  // Modal state management
  const modals = ref({
    taskCreate: false,
    accountSettings: false,
  })

  // Generic modal functions
  const openModal = (modalName: keyof typeof modals.value) => {
    modals.value[modalName] = true
  }

  const closeModal = (modalName: keyof typeof modals.value) => {
    modals.value[modalName] = false
  }

  // Specific modal handlers
  const taskModalSubmit = (taskData: {
    taskName: string
    taskDescription: string | null
    deadlineDate: string | null
  }) => {
    closeModal('taskCreate')
    fetch(`${import.meta.env.VITE_SERVER}/tasks`, {
      method: `POST`,
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        name: taskData.taskName,
        date_deadline: taskData.deadlineDate,
        date_completed: null,
        current_column: 'pool',
      }),
    })
      .then((response) => {
        if (!response.ok) {
          throw new Error(`Error creating task: ${response.status} ${response.text}`)
        }
        return response.json
      })
      .then((data) => {
        console.log(data)
      })
  }

  const accountModalSubmit = (accountData: {
    userName: string
    userPassword: string
    currentPassword: string | null
    newPassword: string | null
  }) => {
    closeModal('accountSettings')
    if (accountData.userName && accountData.userPassword) {
      fetch(`${import.meta.env.VITE_SERVER}/auth/login`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          username: accountData.userName,
          password: accountData.userPassword,
        }),
      })
        .then((response) => {
          if (!response.ok) {
            alert(`Ошибка: ${response.status} ${response.statusText}`)
            throw new Error(`HTTP error! status: ${response.status}`)
          }
          return response.json()
        })
        .then((body) => {
          console.log(body)
          document.cookie = `user=${JSON.stringify(body.user)}`
        })
    }
  }
</script>

<template>
  <div class="header">
    <input placeholder="Поиск по задачам..." />
    <div class="project-list">
      <button @click="showProjectDropdown = !showProjectDropdown">
        {{ projects.find(p => p.id === currentProject)?.name || 'Проекты' }}
        <span class="arrow" :class="{rotated: showProjectDropdown}">▼</span>
      </button>

      <div v-if="showProjectDropdown" class="dropdown-content">
        <div v-for="project in projects"
             :key="project.id"
             @click="currentProject = project.id; showProjectDropdown = false"
             :class="{active: project.id === currentProject}">
          {{ project.name }}
        </div>
      </div>
    </div>
    <button @click="() => openModal('taskCreate')">Создать задачу</button>
    <button @click="() => openModal('accountSettings')">Аккаунт</button>
  </div>
  <TopBarTaskCreate :is-open="modals.taskCreate"
                    @modal-close="() => closeModal('taskCreate')"
                    @modal-submit="taskModalSubmit"
                    name="first-modal"></TopBarTaskCreate>
  <TopBarAccountSettings :is-open="modals.accountSettings"
                         @modal-close="() => closeModal('accountSettings')"
                         @modal-submit="accountModalSubmit"
                         name="account-modal"></TopBarAccountSettings>
</template>

<style lang="css" scoped>
  .header {
    display: flex;
    justify-content: center;
    position: fixed;
    width: 100%;
    height: 10%;
    background-color: #28b6f8;
  }

    .header input {
      font-size: 15px;
      margin-top: 20px;
      margin-left: 20px;
      margin-right: auto;
      width: 30%;
      height: 30%;
      border-radius: 5px;
      border: none;
      outline-color: #1b84a7;
    }

    .header button {
      background-color: #ffffff;
      border: none;
      border-radius: 5px;
      height: 50%;
      width: 10%;
      margin-right: 3%;
      margin-top: 1.3%;
      font-size: 15px;
      cursor: pointer;
      transition: all 0.2s;
    }

      .header button:hover {
        background-color: #e6e6e6;
      }

.project-list {
      margin-right: 3%;
      margin-top: 1.4%;
}

  .project-list > button {
    background-color: #28b6f8;
    border: none;
    height: 50%;
    min-width: 100px;
    padding: 0 15px;
    cursor: pointer;
    display: flex;
    align-items: center;
    font-size: 15px;
  }

    .project-list > button:hover {
      background-color: #28b6f8;
    }

  .dropdown-content {
    position: absolute;
    background-color: #f9f9f9;
    min-width: 200px;
    box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
    z-index: 1;
    border-radius: 5px;
    max-height: 300px;
    overflow-y: auto;
  }

    .dropdown-content div {
      padding: 12px 16px;
      cursor: pointer;
    }

      .dropdown-content div:hover {
        background-color: #e6e6e6;
      }

    .dropdown-content .active {
      background-color: #28b6f8;
      color: white;
    }

  .arrow {
    font-size: 15px;
    margin-left: 8px;
  }

  .project-list > button .arrow {
    transition: transform 0.2s ease;
    display: inline-block;
  }

    .project-list > button .arrow.rotated {
      transform: rotate(90deg);
    }

  @media (max-width: 768px) {
    .header {
      padding: 10px;
    }

      .header input {
        order: 1;
        flex: 1 1 100%;
        margin: 5px 0;
      }
  }
</style>
