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
        <div><strong>{{ trap.timestamp }}</strong></div>
        <span v-if="trap.type"> Se ha recibido una trap de tipo {{ trap.type }}</span>
        <span v-if="trap.source"> – desde {{ trap.source }}</span>
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

// Diccionario OID → nombre de trap
const TRAP_TYPES = {
  '1.3.6.1.6.3.1.1.5.1':   'coldStart',
  '1.3.6.1.6.3.1.1.5.2':   'warmStart',
  '1.3.6.1.6.3.1.1.5.3':   'linkDown',
  '1.3.6.1.6.3.1.1.5.4':   'linkUp',
  '1.3.6.1.6.3.1.1.5.5':   'nsNotifyShutdown',
  '1.3.6.1.6.3.1.1.5.6':   'egpNeighborLoss',
  '1.3.6.1.6.3.1.1.5.7':   'enterpriseSpecific',
  // …
}

onMounted(() => {
  // Ajusta la URL si tu backend corre en otra dirección o puerto:
  const url = 'http://localhost:8000/traps/stream'
  const es = new EventSource(url)

  es.onmessage = evt => {
    const data = JSON.parse(evt.data)
    data.timestamp = new Date().toLocaleString()

    // 2. Formatear source (si viene separado)
    if (data.source_ip && data.source_port) {
      data.source = `${data.source_ip}:${data.source_port}`
    }

    // 3. Extraer el valor de snmpTrapOID.0
    const trapOidVar = data.varBinds.find(vb =>
      vb.oid === '1.3.6.1.6.3.1.1.4.1.0'
    )
    const trapOidVal = trapOidVar?.value

    // 4. Mapear al nombre legible
    data.type = TRAP_TYPES[trapOidVal] || 'unknownTrap'

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

