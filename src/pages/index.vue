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
const filter = ref<'all' | 'active' | 'done'>('all')

const filteredTodos = computed(() => {
  if (filter.value === 'active')
    return todos.value.filter(t => !t.done)
  if (filter.value === 'done')
    return todos.value.filter(t => t.done)
  return todos.value
})

const progress = computed(() => {
  if (!todos.value.length)
    return 0
  return Math.round((todos.value.filter(t => t.done).length / todos.value.length) * 100)
})

function addTodo() {
  if (!newTodo.value.trim())
    return
  todos.value.push({ id: Date.now(), text: newTodo.value.trim(), done: false })
  newTodo.value = ''
}

function removeTodo(todo: Todo) {
  todos.value.splice(todos.value.indexOf(todo), 1)
}

function toggleTodo(todo: Todo) {
  todo.done = !todo.done
}
</script>

<template>
  <div class="px-4 pb-24 pt-12 flex min-h-screen w-full items-start justify-center">
    <div class="max-w-md w-full">
      <!-- Header -->
      <div class="mb-6">
        <div class="mb-1 flex gap-3 items-center">
          <div i-carbon-checkbox-checked-filled class="text-2xl text-violet-600 dark:text-violet-400" />
          <h1 class="text-2xl text-gray-900 tracking-tight font-bold dark:text-white">
            My Tasks
          </h1>
        </div>
        <p class="text-sm text-gray-500 pl-9 dark:text-gray-400">
          {{ todos.filter(t => !t.done).length }} remaining · {{ todos.filter(t => t.done).length }} completed
        </p>

        <!-- Progress bar -->
        <div v-if="todos.length" class="mt-4 pl-9">
          <div class="text-xs text-gray-400 mb-1.5 flex justify-between dark:text-gray-500">
            <span>Progress</span>
            <span class="text-violet-600 font-medium dark:text-violet-400">{{ progress }}%</span>
          </div>
          <div class="rounded-full bg-gray-200 h-1.5 overflow-hidden dark:bg-gray-700">
            <div
              class="rounded-full bg-violet-500 h-full transition-all duration-700 ease-out"
              :style="{ width: `${progress}%` }"
            />
          </div>
        </div>
      </div>

      <!-- Main Card -->
      <div class="border border-gray-200 rounded-2xl bg-white shadow-sm overflow-hidden dark:border-gray-700 dark:bg-gray-900">
        <!-- Input -->
        <div class="p-4 border-b border-gray-100 dark:border-gray-800">
          <div class="flex gap-2">
            <input
              v-model="newTodo"
              type="text"
              placeholder="What needs to be done?"
              class="text-sm text-gray-900 px-4 py-2.5 outline-none border border-gray-200 rounded-xl bg-gray-50 flex-1 transition-all duration-200 dark:text-gray-100 dark:border-gray-700 focus:border-violet-500 dark:bg-gray-800 focus:ring-2 focus:ring-violet-500/20 dark:focus:border-violet-500 placeholder-gray-400 dark:placeholder-gray-500"
              @keydown.enter="addTodo"
            >
            <button
              class="text-sm text-white font-semibold px-4 py-2.5 rounded-xl bg-violet-600 flex gap-1.5 whitespace-nowrap transition-all duration-150 items-center hover:bg-violet-700 active:scale-95"
              @click="addTodo"
            >
              <div i-carbon-add class="text-base" />
              Add
            </button>
          </div>
        </div>

        <!-- Filter Tabs -->
        <div v-if="todos.length" class="border-b border-gray-100 flex dark:border-gray-800">
          <button
            v-for="tab in (['all', 'active', 'done'] as const)"
            :key="tab"
            class="text-xs tracking-wide font-semibold py-2.5 flex-1 uppercase transition-all duration-150"
            :class="filter === tab
              ? 'text-violet-600 dark:text-violet-400 border-b-2 border-violet-500 bg-violet-50 dark:bg-violet-950/30'
              : 'text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200 hover:bg-gray-50 dark:hover:bg-gray-800/50'"
            @click="filter = tab"
          >
            {{ tab }}
          </button>
        </div>

        <!-- Todo List -->
        <div class="max-h-96 overflow-y-auto divide-gray-100 divide-y dark:divide-gray-800">
          <!-- Empty state -->
          <div v-if="filteredTodos.length === 0" class="text-gray-400 py-12 flex flex-col items-center justify-center dark:text-gray-600">
            <div i-carbon-face-satisfied class="text-3xl mb-2" />
            <p class="text-sm">
              {{ filter === 'all' ? 'No tasks yet. Enjoy your day!' : `No ${filter} tasks.` }}
            </p>
          </div>

          <div
            v-for="todo in filteredTodos"
            :key="todo.id"
            class="group px-4 py-3.5 flex gap-3 transition-colors duration-100 items-center hover:bg-gray-50 dark:hover:bg-gray-800/50"
          >
            <!-- Checkbox -->
            <button
              class="border-2 rounded-full flex flex-shrink-0 h-5 w-5 transition-all duration-200 items-center justify-center"
              :class="todo.done
                ? 'bg-violet-500 border-violet-500'
                : 'border-gray-300 dark:border-gray-600 hover:border-violet-400 dark:hover:border-violet-500'"
              @click="toggleTodo(todo)"
            >
              <div v-if="todo.done" i-carbon-checkmark class="text-xs text-white" />
            </button>

            <!-- Text -->
            <span
              class="text-sm leading-relaxed text-left flex-1 break-all transition-all duration-200"
              :class="todo.done ? 'line-through text-gray-400 dark:text-gray-500' : 'text-gray-800 dark:text-gray-100'"
            >
              {{ todo.text }}
            </span>

            <!-- Delete -->
            <button
              class="text-gray-400 p-1 rounded-lg opacity-0 transition-all duration-150 dark:text-gray-500 hover:text-red-500 group-hover:opacity-100 dark:hover:text-red-400"
              @click="removeTodo(todo)"
            >
              <div i-carbon-trash-can class="text-base" />
            </button>
          </div>
        </div>

        <!-- Footer -->
        <div v-if="todos.length" class="px-4 py-3 border-t border-gray-100 bg-gray-50 flex items-center justify-between dark:border-gray-800 dark:bg-gray-800/40">
          <span class="text-xs text-gray-500 dark:text-gray-400">
            {{ todos.filter(t => !t.done).length }} item{{ todos.filter(t => !t.done).length !== 1 ? 's' : '' }} left
          </span>
          <button
            v-if="todos.some(t => t.done)"
            class="text-xs text-gray-500 font-medium transition-colors duration-150 dark:text-gray-400 hover:text-red-500 dark:hover:text-red-400"
            @click="todos = todos.filter(t => !t.done)"
          >
            Clear completed
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
