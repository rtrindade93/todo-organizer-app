<script setup lang="ts">
import { onMounted, ref, type Ref } from 'vue';
import { Todo } from '@/models/todo.model';
import axios from 'axios';

const todos: Ref<Todo[]> = ref([]);
const newTodo: Ref<string> = ref('');

const addTodo = async () => {
  const todoToAdd = new Todo(todos.value[todos.value.length - 1].id + 1, newTodo.value);
  await axios.post('http://localhost:5000/todos', todoToAdd);
  todos.value.push(todoToAdd);
  newTodo.value = '';
}

const completeTodo = async (todo: Todo) => {
  todo.done = !todo.done;
  await axios.put(`http://localhost:5000/todos/${todo.id}`, todo);
  const foundTodo = todos.value.find(t => todo.id === t.id);
  if (foundTodo) {
    foundTodo.done = todo.done;
  }
}

onMounted(async () => {
  const response = await axios.get('http://localhost:5000/todos');
  todos.value = response.data;
});
</script>

<template>
   <section class="py-4">
    <div class="container-md lg:container m-auto">
      <div class="flex justify-center items-center">
        <h1 class="text-2xl font-bold">ToDos</h1>
      </div>
      <div class="mt-4">
        <ul class="divide-y divide-gray-200">
          <li v-for="todo in todos" :key="todo.id" class="py-4">
            <input @change="completeTodo(todo)" v-model="todo.done" type="checkbox" class="mr-2" />
            {{ todo.text }}
          </li>
          <li class="py-4 grid grid-cols-[70%_30%] gap-4">
            <input v-model="newTodo" type="text" placeholder="Add a new todo" />
            <button @click="addTodo" class="mt-2 bg-blue-500 text-white px-4 py-2 rounded">Add</button>
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>
