<script setup lang="ts">
import { onMounted, ref, type Ref } from 'vue';
import { useToast } from 'vue-toastification'
import { Todo } from '@/models/todo.model';
import axios from 'axios';

const toast = useToast();

const todos: Ref<Todo[]> = ref([]);
const newTodo: Ref<string> = ref('');

const getTodos = async (): Promise<void> => {
  try {
    const response = await axios.get('/todos');
    todos.value = response.data;
  } catch (error) {
    console.error('Error getting To Do List', error);
    toast.error('Error getting To Do List!');
  }
}

const addTodo = async (): Promise<void> => {
  try {
    const todoToAdd = new Todo(`${Number(todos.value[todos.value.length - 1].id) + 1}`, newTodo.value);
    await axios.post('/todos', todoToAdd);
    todos.value.push(todoToAdd);
    newTodo.value = '';

    toast.success('To Do successfully added!');
  } catch (error) {
    console.error('Error adding To Do', error);
    toast.error('To Do not added!');
  }
}

const completeTodo = async (todoId: Integer): Promise<void> => {
  try {
    const foundTodo = todos.value.find(t => todoId === t.id);
    if (foundTodo) {
      await axios.put(`/todos/${todoId}`, foundTodo);

      toast.success('To Do updated successfully!');
    }
  } catch (error) {
    console.error('Error updating To Do', error);
    toast.error('To Do not updated!');
  }
}

onMounted(async () => {
  await getTodos();
});
</script>

<template>
  <section class="py-4">
    <div class="container-sm sm:container m-auto">
      <div class="flex justify-center items-center">
        <h1 class="text-2xl font-bold">ToDos</h1>
      </div>
      <div class="mt-4">
        <ul class="divide-y divide-gray-200">
          <li v-for="todo in todos" :key="todo.id" class="py-4">
            <input @change="completeTodo(todo.id)" v-model="todo.done" type="checkbox" class="mr-2" />
            {{ todo.text }}
          </li>
          <li class="py-4 grid grid-cols-[70%_30%] gap-4">
            <input v-model="newTodo" class="block text-sm font-medium text-gray-700 placeholder:text-gray-500 pl-[14px]"
              type="text" placeholder="Add a new todo" />
            <button type="button" @click.prevent="addTodo"
              class="mt-2 bg-gray-800 text-white px-4 py-2 rounded">Add</button>
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>
