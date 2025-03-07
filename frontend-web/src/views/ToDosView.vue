<script setup lang="ts">
import ToDosComponent from '@/components/ToDosComponent.vue';
import { Todo } from '@/models/todo.model';
import axios from 'axios';
import { onMounted, type Ref, ref } from 'vue';
import { useToast } from 'vue-toastification';

const toast = useToast();
const filterOption: Ref<string> = ref('');
const todos: Ref<Todo[]> = ref([]);

const changeFilterOption = async (option: string): Promise<void> => {
  filterOption.value = option;
  await getTodos();

  switch (option) {
    case 'todo':
      todos.value = todos.value.filter(t => !t.done);
      break;
    case 'done':
      todos.value = todos.value.filter(t => t.done);
      break;
    case 'all':
      break;
  }
}

const getTodos = async (): Promise<void> => {
  try {
    const response = await axios.get('/todos');
    todos.value = response.data;
  } catch (error) {
    console.error('Error getting To Do List', error);
    toast.error('Error getting To Do List!');
  }
}

const addTodo = async (newTodo: string): Promise<void> => {
  try {
    const todoToAdd = new Todo(`${Number(todos.value[todos.value.length - 1].id) + 1}`, newTodo);
    await axios.post('/todos', todoToAdd);
    todos.value.push(todoToAdd);

    toast.success('To Do successfully added!');
  } catch (error) {
    console.error('Error adding To Do', error);
    toast.error('To Do not added!');
  }
}

const completeTodo = async (todoId: string): Promise<void> => {
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

const deleteTodo = async (todoId: string): Promise<void> => {
  try {
    await axios.delete(`/todos/${todoId}`);
    todos.value = todos.value.filter(t => t.id !== todoId);

    toast.success('To Do deleted successfully!');
  } catch (error) {
    console.error('Error deleting To Do', error);
    toast.error('To Do not deleted!');
  }
}

onMounted(() => {
  changeFilterOption('todo');
});
</script>

<template>
  <section class="py-4">
    <div class="container-sm sm:container m-auto">
      <ul
        class="flex flex-wrap text-sm font-medium text-center text-gray-500 border-b border-gray-200 dark:border-gray-700 dark:text-gray-400">
        <li class="me-2">
          <a type="button" aria-current="page"
            :class="[filterOption === 'todo' ? 'bg-gray-400' : 'bg-gray-100', 'inline-block', 'p-4', 'text-gray-600', 'rounded-t-lg', 'hover:bg-gray-400']"
            @click="changeFilterOption('todo')">
            To Do's
          </a>
        </li>
        <li class="me-2">
          <a type="button" aria-current="page"
            :class="[filterOption === 'done' ? 'bg-gray-400' : 'bg-gray-100', 'inline-block', 'p-4', 'text-gray-600', 'rounded-t-lg', 'hover:bg-gray-400']"
            @click="changeFilterOption('done')">
            Done
          </a>
        </li>
        <li class="me-2">
          <a type="button" aria-current="page"
            :class="[filterOption === 'all' ? 'bg-gray-400' : 'bg-gray-100', 'inline-block', 'p-4', 'text-gray-600', 'rounded-t-lg', 'hover:bg-gray-400']"
            @click="changeFilterOption('all')">
            All
          </a>
        </li>
      </ul>

      <ToDosComponent :canAdd="filterOption === 'todo'" :todos="todos" @addTodo="addTodo" @completeTodo="completeTodo"
        @deleteTodo="deleteTodo" />
    </div>
  </section>
</template>
