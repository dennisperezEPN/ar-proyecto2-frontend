<template>
  <div id="app">
    <header class="app-header">
      <h1> Network Management System</h1>
    </header>

    <div class="app-body">
      <!-- Sidebar recibe selected y emite update:selected -->
      <AppSidebar
        :selected="selected"
        @update:selected="selected = $event"
      />

      <!-- MainContainer recibe selected para mostrar la vista adecuada -->
      <MainContainer :selected="selected" />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import AppSidebar   from './components/AppSidebar.vue'
import MainContainer from './components/MainContainer.vue'

// Estado reactivo que guarda la "pantalla" actual.
// Coincide con los `key` de tus items en AppSidebar.
const selected = ref('home')
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

:root {
  --bg-dark: #1f1f1f;
  --text-light: #e0e0e0;
  --accent-green: #4caf50;
  --bg-panel: #2c2c2c;
  --hover-green: #357a38;
  --font-family: 'Inter', sans-serif;
}

#app {
  display: flex;
  flex-direction: column;
  height: 100vh;
  margin: 0;
  background: var(--bg-dark);
  color: var(--text-light);
  font-family: var(--font-family);
}

.app-header {
  background: var(--bg-panel);
  border-bottom: 1px solid var(--accent-green);
  padding: 1rem;
}

.app-header h1 {
  margin: 0;
  font-size: 1.5rem;
}

.app-body {
  display: flex;
  flex: 1;
}
</style>

