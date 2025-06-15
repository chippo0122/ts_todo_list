<!-- @format -->

<template>
  <li
    class="border border-white border-solid rounded-md p-3 mb-2 flex justify-between items-center pb-3"
  >
    <div class="flex items-center gap-2">
      <input
        type="checkbox"
        :checked="!!todo.finished"
        @change="$emit('toggle', todo.id)"
        class="cursor-pointer"
      />
      <div>
        <p class="text-lg" :class="{ 'line-through': todo.finished }">
          {{ todo.content }}
        </p>
        <p class="text-sm" v-if="todo.finished !== null">
          Finished at: {{ new Date(todo.finished).toLocaleString() }}
        </p>
      </div>
    </div>
    <button
      @click="$emit('remove', todo.id)"
      class="text-red-500 hover:text-red-700"
    >
      Remove
    </button>
  </li>
</template>

<script setup lang="ts">
import { type Todo } from "../types/todo"

defineProps<{
  todo: Todo
}>()

defineEmits<{
  (e: "remove", id: number): void
  (e: "toggle", id: number): void
}>()
</script>
