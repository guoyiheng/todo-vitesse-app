<script setup lang="ts">
import { useStorage } from '@vueuse/core'

defineOptions({
  name: 'IndexPage',
})

interface Todo {
  id: number
  text: string
  done: boolean
}

const newTodo = ref('')
const todos = useStorage<Todo[]>('todos-vitesse', [])

function addTodo() {
  if (!newTodo.value.trim())
    return

  todos.value.push({
    id: Date.now(),
    text: newTodo.value,
    done: false,
  })
  newTodo.value = ''
}

function removeTodo(todo: Todo) {
  const index = todos.value.indexOf(todo)
  if (index > -1)
    todos.value.splice(index, 1)
}

function toggleTodo(todo: Todo) {
  todo.done = !todo.done
}
</script>

<template>
  <div class="max-w-md mx-auto py-10 px-4">
    <div i-carbon-notebook text-4xl inline-block class="mb-4 text-primary" />
    <h1 class="text-3xl font-bold mb-6 text-gray-800 dark:text-gray-100">
      My Awesome Tasks
    </h1>

    <div class="flex gap-2 mb-8">
      <input
        v-model="newTodo"
        type="text"
        placeholder="Add a new task..."
        class="flex-1 px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 focus:outline-none focus:ring-2 focus:ring-primary bg-white dark:bg-gray-800"
        @keydown.enter="addTodo"
      >
      <button
        class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg transition-colors duration-200 flex items-center gap-2"
        @click="addTodo"
      >
        <div i-carbon-add />
        Add
      </button>
    </div>

    <div v-if="todos.length === 0" class="text-center text-gray-500 py-10 opacity-75">
      <div i-carbon-face-satisfied text-4xl inline-block mb-2 />
      <p>No tasks yet. Enjoy your day!</p>
    </div>

    <ul v-else class="space-y-3">
      <li
        v-for="todo in todos"
        :key="todo.id"
        class="flex items-center gap-3 p-3 bg-white dark:bg-gray-800 rounded-lg shadow-sm border border-gray-200 dark:border-gray-700 hover:shadow-md transition-shadow duration-200"
      >
        <button
          class="flex-shrink-0 w-6 h-6 rounded border-2 flex items-center justify-center transition-colors"
          :class="todo.done ? 'bg-green-500 border-green-500 text-white' : 'border-gray-300 dark:border-gray-600 hover:border-green-500'"
          @click="toggleTodo(todo)"
        >
          <div v-if="todo.done" i-carbon-checkmark text-sm />
        </button>
        
        <span
          class="flex-1 text-left break-all transition-all duration-200"
          :class="{ 'line-through text-gray-400': todo.done }"
        >
          {{ todo.text }}
        </span>

        <button
          class="text-gray-400 hover:text-red-500 p-1 rounded transition-colors"
          @click="removeTodo(todo)"
        >
          <div i-carbon-trash-can text-lg />
        </button>
      </li>
    </ul>

    <div class="mt-8 text-sm text-gray-400 flex justify-between">
      <span>{{ todos.filter(t => !t.done).length }} items left</span>
      <button 
        v-if="todos.some(t => t.done)"
        class="hover:text-red-500 hover:underline"
        @click="todos = todos.filter(t => !t.done)"
      >
        Clear completed
      </button>
    </div>
  </div>
</template>
