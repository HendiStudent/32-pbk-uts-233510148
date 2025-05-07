<template>
  <h1>{{ h1 }}</h1>
  <form @submit.prevent="addTodo">
    <input v-model="newTodo" required placeholder="New todo" type="text" />
    <button>Tambah Tugas</button>
  </form>

  <ul>
    <li v-for="todo in filteredTodos" :key="todo.id">
      <input type="checkbox" v-model="todo.done" @change="saveTodos" />
      <span :class="{ done: todo.done }">{{ todo.text }}</span>
      <button @click="removeTodo(todo)">🗑</button>
    </li>
  </ul>

  <div class="icon">
    <button @click="hideCompleted = !hideCompleted">
      {{ hideCompleted ? 'Tampilkan semua' : 'Tampilkan selesai' }}
    </button>
  </div>

  <footer>
  &copy; 2025 My Todo List. @Hendi Syaputra.
</footer>

</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

let id = 0
const h1 = ref('My Todo List')
const newTodo = ref('')
const hideCompleted = ref(false)

// Ambil dari localStorage jika ada
const todos = ref([])

onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) {
    const parsed = JSON.parse(saved)
    todos.value = parsed
    // Update id supaya tidak duplikat
    id = parsed.length > 0 ? Math.max(...parsed.map(t => t.id)) + 1 : 0
  } else {
    // Jika belum ada data, gunakan data default
    todos.value = [
      { id: id++, text: 'Belajar DataBase', done: true },
      { id: id++, text: 'Belajar Pemrograman', done: true },
      { id: id++, text: 'Belajar VueJs', done: false }
    ]
  }
})

watch(todos, saveTodos, { deep: true })

function saveTodos() {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

const filteredTodos = computed(() => {
  return hideCompleted.value
    ? todos.value.filter((t) => !t.done)
    : todos.value
})

function addTodo() {
  if (!newTodo.value.trim()) return
  todos.value.push({ id: id++, text: newTodo.value.trim(), done: false })
  newTodo.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo)
}
</script>

<style>
.done {
  text-decoration: line-through;
  color: #888;
}
</style>

