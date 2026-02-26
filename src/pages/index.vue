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
  if (filter.value === 'active') return todos.value.filter(t => !t.done)
  if (filter.value === 'done') return todos.value.filter(t => t.done)
  return todos.value
})

const progress = computed(() => {
  if (!todos.value.length) return 0
  return Math.round((todos.value.filter(t => t.done).length / todos.value.length) * 100)
})

function addTodo() {
  if (!newTodo.value.trim()) return
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
  <div class="min-h-screen w-full flex items-start justify-center pt-12 pb-24 px-4">
    <div class="w-full max-w-md">
      <!-- Header -->
      <div class="mb-6">
        <div class="flex items-center gap-3 mb-1">
          <div i-carbon-checkbox-checked-filled class="text-violet-600 dark:text-violet-400 text-2xl" />
          <h1 class="text-2xl font-bold text-gray-900 dark:text-white tracking-tight">
            My Tasks
          </h1>
        </div>
        <p class="text-sm text-gray-500 dark:text-gray-400 pl-9">
          {{ todos.filter(t => !t.done).length }} remaining · {{ todos.filter(t => t.done).length }} completed
        </p>

        <!-- Progress bar -->
        <div v-if="todos.length" class="mt-4 pl-9">
          <div class="flex justify-between text-xs text-gray-400 dark:text-gray-500 mb-1.5">
            <span>Progress</span>
            <span class="font-medium text-violet-600 dark:text-violet-400">{{ progress }}%</span>
          </div>
          <div class="h-1.5 bg-gray-200 dark:bg-gray-700 rounded-full overflow-hidden">
            <div
              class="h-full bg-violet-500 rounded-full transition-all duration-700 ease-out"
              :style="{ width: `${progress}%` }"
            />
          </div>
        </div>
      </div>

      <!-- Main Card -->
      <div class="rounded-2xl bg-white dark:bg-gray-900 border border-gray-200 dark:border-gray-700 shadow-sm overflow-hidden">
        <!-- Input -->
        <div class="p-4 border-b border-gray-100 dark:border-gray-800">
          <div class="flex gap-2">
            <input
              v-model="newTodo"
              type="text"
              placeholder="What needs to be done?"
              class="flex-1 bg-gray-50 dark:bg-gray-800 text-gray-900 dark:text-gray-100 placeholder-gray-400 dark:placeholder-gray-500 rounded-xl px-4 py-2.5 text-sm outline-none border border-gray-200 dark:border-gray-700 focus:border-violet-500 dark:focus:border-violet-500 focus:ring-2 focus:ring-violet-500/20 transition-all duration-200"
              @keydown.enter="addTodo"
            >
            <button
              class="bg-violet-600 hover:bg-violet-700 active:scale-95 text-white rounded-xl px-4 py-2.5 text-sm font-semibold flex items-center gap-1.5 transition-all duration-150 whitespace-nowrap"
              @click="addTodo"
            >
              <div i-carbon-add class="text-base" />
              Add
            </button>
          </div>
        </div>

        <!-- Filter Tabs -->
        <div v-if="todos.length" class="flex border-b border-gray-100 dark:border-gray-800">
          <button
            v-for="tab in (['all', 'active', 'done'] as const)"
            :key="tab"
            class="flex-1 py-2.5 text-xs font-semibold tracking-wide uppercase transition-all duration-150"
            :class="filter === tab
              ? 'text-violet-600 dark:text-violet-400 border-b-2 border-violet-500 bg-violet-50 dark:bg-violet-950/30'
              : 'text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200 hover:bg-gray-50 dark:hover:bg-gray-800/50'"
            @click="filter = tab"
          >
            {{ tab }}
          </button>
        </div>

        <!-- Todo List -->
        <div class="divide-y divide-gray-100 dark:divide-gray-800 max-h-96 overflow-y-auto">
          <!-- Empty state -->
          <div v-if="filteredTodos.length === 0" class="flex flex-col items-center justify-center py-12 text-gray-400 dark:text-gray-600">
            <div i-carbon-face-satisfied class="text-3xl mb-2" />
            <p class="text-sm">
              {{ filter === 'all' ? 'No tasks yet. Enjoy your day!' : `No ${filter} tasks.` }}
            </p>
          </div>

          <div
            v-for="todo in filteredTodos"
            :key="todo.id"
            class="group flex items-center gap-3 px-4 py-3.5 hover:bg-gray-50 dark:hover:bg-gray-800/50 transition-colors duration-100"
          >
            <!-- Checkbox -->
            <button
              class="flex-shrink-0 w-5 h-5 rounded-full border-2 flex items-center justify-center transition-all duration-200"
              :class="todo.done
                ? 'bg-violet-500 border-violet-500'
                : 'border-gray-300 dark:border-gray-600 hover:border-violet-400 dark:hover:border-violet-500'"
              @click="toggleTodo(todo)"
            >
              <div v-if="todo.done" i-carbon-checkmark class="text-white text-xs" />
            </button>

            <!-- Text -->
            <span
              class="flex-1 text-sm text-left break-all leading-relaxed transition-all duration-200"
              :class="todo.done ? 'line-through text-gray-400 dark:text-gray-500' : 'text-gray-800 dark:text-gray-100'"
            >
              {{ todo.text }}
            </span>

            <!-- Delete -->
            <button
              class="opacity-0 group-hover:opacity-100 text-gray-400 dark:text-gray-500 hover:text-red-500 dark:hover:text-red-400 p-1 rounded-lg transition-all duration-150"
              @click="removeTodo(todo)"
            >
              <div i-carbon-trash-can class="text-base" />
            </button>
          </div>
        </div>

        <!-- Footer -->
        <div v-if="todos.length" class="flex items-center justify-between px-4 py-3 bg-gray-50 dark:bg-gray-800/40 border-t border-gray-100 dark:border-gray-800">
          <span class="text-xs text-gray-500 dark:text-gray-400">
            {{ todos.filter(t => !t.done).length }} item{{ todos.filter(t => !t.done).length !== 1 ? 's' : '' }} left
          </span>
          <button
            v-if="todos.some(t => t.done)"
            class="text-xs text-gray-500 dark:text-gray-400 hover:text-red-500 dark:hover:text-red-400 font-medium transition-colors duration-150"
            @click="todos = todos.filter(t => !t.done)"
          >
            Clear completed
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
