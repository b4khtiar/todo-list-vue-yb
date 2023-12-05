<script setup>
import { RouterView } from 'vue-router'
</script>

<template>
  <div class="bg-gray-100">
    <header class="bg-blue-700 py-9" data-cy="header-background">
      <div class="container mx-auto px-2">
        <h2 class="text-3xl font-bold text-white" data-cy="header-title">TO DO LIST APP</h2>
      </div>
    </header>

    <RouterView />
  </div>
</template>
