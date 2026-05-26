<script setup>
import { ref } from 'vue'

const message = ref('Hello, Vue 3 + Vite!')
</script>

<template>
  <main class="app">
    <h1>{{ message }}</h1>
  </main>
</template>

<style scoped>
.app {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  font-family: system-ui, -apple-system, sans-serif;
}
</style>