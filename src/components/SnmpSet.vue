<!-- src/components/SnmpSet.vue -->
<template>
  <div class="snmp-set">
    <h2>SNMP Set</h2>

    <form @submit.prevent="doSnmpSet">
      <!-- Usuario -->
      <div class="form-group">
        <label for="user">Usuario SNMPv3</label>
        <input
          id="user"
          v-model="user"
          type="text"
          placeholder="snmpuser"
          required
        />
      </div>

      <!-- IP -->
      <div class="form-group">
        <label for="ip">IP del dispositivo</label>
        <input
          id="ip"
          v-model="ip"
          type="text"
          placeholder="192.168.1.42"
          pattern="^([0-9]{1,3}\.){3}[0-9]{1,3}$"
          required
        />
      </div>

     <!-- Selector de MIB común -->
      <div class="form-group">
        <label for="mibSelect">MIB común</label>
        <select id="mibSelect" v-model="selectedMib">
          <option value="">— Ninguna —</option>
          <option
            v-for="mib in commonMibs"
            :key="mib.name"
            :value="mib.oid"
          >
            {{ mib.name }}
          </option>
        </select>
      </div>

      <!-- Input para OID específico -->
      <div class="form-group">
        <label for="oid">OID</label>
        <input
          id="oid"
          v-model="oid"
          type="text"
          placeholder="Por ejemplo: 1.3.6.1.2.1.1.5.0"
        />
      </div> 

      <!-- Tipo de dato -->
      <div class="form-group">
        <label for="type">Tipo de dato</label>
        <select id="type" v-model="type">
          <option value="OctetString">OctetString</option>
          <option value="Integer">Integer</option>
          <option value="IpAddress">IpAddress</option>
          <option value="Counter32">Counter32</option>
          <option value="Gauge32">Gauge32</option>
          <option value="TimeTicks">TimeTicks</option>
          <option value="Opaque">Opaque</option>
          <option value="Counter64">Counter64</option>
          <option value="Bits">Bits</option>
        </select>
      </div>

      <!-- Valor -->
      <div class="form-group">
        <label for="value">Valor</label>
        <input
          id="value"
          :type="isNumericType ? 'number' : 'text'"
          v-model="value"
          required
        />
      </div>

      <!-- Botón -->
      <button type="submit" :disabled="loading">
        {{ loading ? 'Enviando...' : 'Enviar SNMP Set' }}
      </button>
    </form>

    <!-- Resultado -->
    <div class="result" v-if="result || error">
      <h3>Resultado</h3>
      <pre v-if="result">{{ result }}</pre>
      <p class="error" v-if="error">{{ error }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

const API_URL = "http://localhost:8000"

export default {
  name: 'SnmpSet',
  data() {
    return {
        ip: '',
        commonMibs: [
            { name: 'MIB-2',   oid: '1.3.6.1.2.1' },
            { name: 'IF-MIB',  oid: '1.3.6.1.2.1.2' },
            { name: 'UCDAVIS',   oid: '1.3.6.1.4.1.2021' },
            // …otras MIBs comunes
        ],
        selectedMib: '',
        oid: '',
        type: 'OctetString',
        value: '',
        loading: false,
        result: null,
        error: null, 
    };
  },
    watch: {
        // Cuando seleccionas una MIB, se rellena automáticamente el OID
        selectedMib(newOid) {
            if (newOid) this.oid = newOid;
        },
    },
  computed: {
    isNumericType() {
      return ['Integer','Counter32','Gauge32','TimeTicks','Counter64']
        .includes(this.type);
    }
  },
  methods: {
    async doSnmpSet() {
      this.loading = true;
      this.error = this.result = null;

      try {
        const res = await axios.post(
            `${API_URL}/snmp/set`,
          {
            user: this.user,
            ip: this.ip,
            oid: this.oid,
            value: this.value,
            type: this.type,
          }
        );
        this.result = JSON.stringify(res.data.snmp_set_result, null, 2);
      } catch (e) {
        this.error = e.response?.data?.detail || e.message;
      } finally {
        this.loading = false;
      }
    }
  }
};
</script>

<style scoped>
.snmp-set {
  max-width: 500px;
  margin: auto;
}
.form-group {
  margin-bottom: 1rem;
}
input, select, button {
  width: 100%;
  padding: 0.5rem;
  font-size: 1rem;
}
button {
  cursor: pointer;
}
.result {
  margin-top: 1.5rem;
  background: #f9f9f9;
  padding: 1rem;
  color: #333;
}
.error {
  color: #c00;
}
</style>

