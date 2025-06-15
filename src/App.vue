<!-- @format -->
<template>
  <div class="todo-list">
    <div class="mx-auto max-w-[820px] px-5">
      <h1 class="text-2xl py-10 text-center text-white">TODO LIST</h1>
      <div class="insert flex gap-3">
        <input
          type="text"
          v-model="inputValue"
          class="border border-gray-300 rounded-md p-2 w-full text-white"
          placeholder="Add a new task..."
        />
        <button @click="addTodo">Add</button>
      </div>

      <div
        class="relative progress border border-white border-solid rounded-md mt-3 h-[24px]"
      >
        <div
          v-if="todos.length > 0"
          class="bg-green-500 h-full animate-progress"
          :style="{ width: `${(doneNumber / todos.length) * 100}%` }"
        ></div>
        <p v-else class="text-white text-center">
          no tasks yet, please add some!
        </p>
        <p
          v-if="checkAllDone"
          class="text-white text-center absolute top-0 left-0 right-0"
        >
          All tasks completed!
        </p>
      </div>

      <ul v-if="todos.length > 0" class="pt-3">
        <Item
          v-for="todo in todos"
          :key="todo.id"
          :todo="todo"
          @remove="(id) => removeTodo(id)"
          @toggle="(id) => toggleFinish(id)"
        />
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from "vue"
import { type Todo } from "./types/todo"
import Item from "./components/TodoItem.vue"

const inputValue = ref<string>("")
const todos = ref<Todo[]>([])

const addTodo = (): void => {
  if (inputValue.value.trim() === "") {
    return
  }

  const newTodo: Todo = {
    id: Date.now(),
    content: inputValue.value,
    finished: null,
  }
  todos.value.push(newTodo)
  inputValue.value = ""
}

const removeTodo = (id: number): void => {
  todos.value = todos.value.filter((todo) => todo.id !== id)
}

const toggleFinish = (id: number): void => {
  if (todos.value.length === 0) return
  todos.value.forEach((todo) => {
    if (todo.id !== id) return
    if (todo.finished === null) {
      const timestamp: number = new Date().getTime()
      todo.finished = timestamp
    } else {
      todo.finished = null
    }
  })
}

const checkAllDone = computed<boolean>(
  () => todos.value.every((todo) => todo.finished) && todos.value.length > 0
)

const doneNumber = computed<number>(
  () => todos.value.filter((todo) => todo.finished).length
)

onMounted(() => {
  const storedTodos: string | null = localStorage.getItem("todos")

  if (storedTodos) {
    try {
      const parsedTodos = JSON.parse(storedTodos)

      if (Array.isArray(parsedTodos)) {
        todos.value = parsedTodos as Todo[]
      } else {
        todos.value = []
      }
    } catch (error) {
      todos.value = []
    }
  } else {
    todos.value = []
  }
})

watch(
  todos,
  (newTodos) => {
    localStorage.setItem("todos", JSON.stringify(newTodos))
  },
  { deep: true }
)
</script>

<style scoped>
.animate-progress {
  transition: width 0.5s ease-in-out;
  animation: breath 2s infinite;
}
@keyframes breath {
  0% {
    opacity: 0.7;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0.7;
  }
}
</style>
