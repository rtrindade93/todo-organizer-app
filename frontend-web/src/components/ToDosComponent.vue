<script setup lang="ts">
import { ref, type Ref } from 'vue';
import { Todo } from '@/models/todo.model';

const props = defineProps({
  canAdd: {
    type: Boolean,
    required: true,
    default: false
  },
  todos: {
    type: Array as () => Todo[],
    required: true
  },
});

const emit = defineEmits(['addTodo', 'completeTodo', 'deleteTodo']);

const newTodo: Ref<string> = ref('');

const completeTodo = (todoId: string): void => {
  emit('completeTodo', todoId);
}

const deleteTodo = (todoId: string): void => {
  emit('deleteTodo', todoId);
}

const addTodo = (): void => {
  emit('addTodo', newTodo.value);
  newTodo.value = '';
}
</script>

<template>
  <div class="flex justify-center items-center">
    <h1 class="text-2xl font-bold">ToDos</h1>
  </div>
  <div class="mt-4">
    <ul class="divide-y divide-gray-200">
      <li v-for="todo in todos" :key="todo.id" class="py-4 grid grid-cols-[5%_90%_5%] gap-4">
        <input @change="completeTodo(todo.id)" v-model="todo.done" type="checkbox" class="mr-2" />
        <span :class="[todo.done ? 'line-through' : '']">{{ todo.text }}</span>
        <button type="button" @click="deleteTodo(todo.id)">
          <i class="pi pi-trash text-gray-800 hover:text-red-600"></i></button>
      </li>
      <li v-if="canAdd" class="py-4 grid grid-cols-[70%_30%] gap-4">
        <input v-model="newTodo" class="block text-sm font-medium text-gray-700 placeholder:text-gray-500 pl-[14px]"
          type="text" placeholder="Add a new todo" />
        <button type="button" @click.prevent="addTodo"
          class="mt-2 bg-gray-800 text-white px-4 py-2 rounded">Add</button>
      </li>
    </ul>
  </div>
</template>
