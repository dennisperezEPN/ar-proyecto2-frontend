<template>
  <aside class="sidebar">
    <ul>
      <li
        v-for="item in items"
        :key="item.key"
        :class="{ active: item.key === selected }"
        @click="select(item.key)"
      >
        {{ item.label }}
      </li>
    </ul>
  </aside>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  selected: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['update:selected'])

const items = [
    {key: 'home', label: 'Home'}, 
    {key: 'get', label: 'Get'}, 
    {key: 'getNext', label: 'GetNext'}, 
    {key: 'set', label: 'Set'}, 
    {key: 'traps', label:'Traps'}
]

function select(item) {
  emit('update:selected', item)
}
</script>

<style scoped>
.sidebar {
  width: 220px;
  background: var(--bg-panel);
  border-right: 1px solid var(--accent-green);
  padding: 1rem;
}

.sidebar ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

.sidebar li {
  padding: 0.75rem 1rem;
  margin-bottom: 0.5rem;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.2s;
}

.sidebar li:hover {
  background: var(--hover-green);
}

.sidebar li.active {
  background: var(--accent-green);
  color: var(--bg-dark);
  font-weight: bold;
}
</style>

