<template>
  <div class="main-container">
    <!-- Montamos dinámicamente el componente según `selected` -->
    <component
      :is="componentMap[selected] || Fallback"
    />
  </div>
</template>

<script setup>
import { defineProps } from 'vue'

// Importa todos los componentes de vista
import HelloWorld   from './HelloWorld.vue'
import SnmpGet      from './SnmpGet.vue'
import SnmpGetNext  from './SnmpGetNext.vue'
import SnmpSet      from './SnmpSet.vue'
import SnmpTraps    from './SnmpTraps.vue'

// Componente para fallback si `selected` no coincide
const Fallback = {
  template: '<p>Selecciona una opción del menú.</p>'
}

// Recibimos la prop `selected`
const props = defineProps({
  selected: { type: String, required: true }
})

// Mapeo de clave → componente
const componentMap = {
  home:     HelloWorld,
  get:      SnmpGet,
  getNext:  SnmpGetNext,
  set:      SnmpSet,
  traps:    SnmpTraps,
}
</script>

<style scoped>
.main-container {
  flex: 1;
  padding: 1.5rem;
  background: var(--bg-dark);
  color: var(--text-light);
}

.main-container h2 {
  margin-top: 0;
  color: var(--accent-green);
}

.content {
  margin-top: 1rem;
  background: var(--bg-panel);
  padding: 1rem;
  border-radius: 4px;
  border: 1px solid var(--accent-green);
}
</style>

