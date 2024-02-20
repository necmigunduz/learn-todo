<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)
const responsible = ref('')

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null || responsible.value.trim() === '') {
    alert("Content, category, or responsible is missing!")
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    responsible: responsible.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })
}

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        How are you doing today,<input type="text" placeholder="your name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a Todo</h3>
      <form @submit.prevent="addTodo">
        <h4>Todo content</h4>
        <input type="text" name="content" id="content" placeholder="e.g. make a video for your blog..."
          v-model="input_content" />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <h4>Responsible</h4>
        <input type="text" name="responsible" id="responsible" placeholder="e.g. Alex, Alicia, ..."
          v-model="responsible" />
        <input type="submit" value="Create a todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for='todo in todos_asc' :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
        </div>
      </div>
    </section>
  </main>
</template>
