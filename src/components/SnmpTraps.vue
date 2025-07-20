<!-- src/components/SnmpTraps.vue -->
<template>
  <div class="snmp-traps">
    <h2>SNMP Traps Recibidas</h2>
    <ul>
      <li
        v-for="(trap, idx) in traps"
        :key="idx"
        class="trap-item"
      >
        <strong>{{ trap.timestamp }}</strong>
        <span v-if="trap.source">– desde {{ trap.source }}</span>
        <ul>
          <li
            v-for="(vb, i2) in trap.varBinds"
            :key="i2"
          >
            {{ vb.oid }} = {{ vb.value }}
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const traps = ref([])

onMounted(() => {
  // Ajusta la URL si tu backend corre en otra dirección o puerto:
  const url = 'http://localhost:8000/traps/stream'
  const es = new EventSource(url)

  es.onmessage = evt => {
    const data = JSON.parse(evt.data)
    data.timestamp = new Date().toLocaleString()
    traps.value.unshift(data)
  }

  es.onerror = err => {
    console.error('Error SSE traps:', err)
    es.close()
  }
})
</script>

<style scoped>
.snmp-traps {
  max-height: 60vh;
  overflow-y: auto;
  padding: .5rem;
}
.trap-item {
  margin-bottom: .75em;
  padding: .5em;
  border: 1px solid var(--accent-green);
  border-radius: 4px;
  background: var(--bg-panel);
}
.trap-item strong {
  color: var(--accent-green);
}
</style>

