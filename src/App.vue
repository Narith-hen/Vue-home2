<template>
  <div>
    <h1 class="text-2xl font-medium text-center">Task Management</h1>
    <input v-model="newTask" @keyup.enter="addTask" placeholder="Enter a task ..." />

    <select v-model="newPriority">
      <option value="Low">Low</option>
      <option value="Medium">Medium</option>
      <option value="High">High</option>
    </select>

    <button @click="addTask">Add</button>

    <ul>
      <li v-for="task in tasks">
        <input type="checkbox" v-model="task.done" />
        <span :class="{ done: task.done }">{{ task.text }}</span>
        <span class="badge" :class="task.priority.toLowerCase()">
          {{ task.priority }}
        </span>
      </li>
    </ul>

    <p>{{ doneCount }}</p>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
const newTask = ref('')
const newPriority = ref('Medium')
const tasks = ref([])

function addTask() {
  if (newTask.value.trim() === '') return

  tasks.value.push({
    id: Date.now(),
    text: newTask.value.trim(),
    priority: newPriority.value,
    done: false,
  })

  newTask.value = ''
  newPriority.value = 'Medium'
}

const doneCount = computed(() => {
  const done = tasks.value.filter((t) => t.done).length
  return `${done} of ${tasks.value.length} done`
})
</script>

<style lang="scss" scoped>
.badge {
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 0.8rem;
}
.low {
  background-color: #4caf50;
  color: white;
}
.medium {
  background-color: #ff9800;
  color: white;
}
.high {
  background-color: #f44336;
  color: white;
}
.done {
  text-decoration: line-through;
  opacity: 0.5;
}
</style>