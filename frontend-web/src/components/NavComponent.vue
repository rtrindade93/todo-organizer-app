<script setup lang="ts">
import { RouterLink, useRoute } from 'vue-router'

const isActive = (path: string): boolean => useRoute().path === path
</script>

<template>
  <nav class="bg-gray-300 border-b">
    <div class="mx-auto max-w-7xl px-2 sm:px-6 lg:px-8">
      <div class="flex h-20 items-center justify-between">
        <div class="flex flex-1 items-center justify-center md:items-stretch md:justify-start">
          <RouterLink
            to="/"
            :class="[
              isActive('/') ? 'bg-gray-500' : '',
              'text-black',
              'hover:bg-gray-700',
              'hover:text-white',
              'px-3',
              'py-2',
              'rounded-md',
            ]"
            >To Do</RouterLink
          >
        </div>
      </div>
    </div>
  </nav>
</template>
