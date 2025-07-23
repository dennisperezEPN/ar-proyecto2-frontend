<!-- src/components/SnmpGet.vue -->
<template>
  <div class="snmp-get">
    <h2>SNMP Get</h2>

    <form @submit.prevent="doSnmpGet">
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
        <label for="ip">Dirección IP</label>
        <input
          id="ip"
          v-model="ip"
          type="text"
          placeholder="192.168.1.42"
          pattern="^([0-9]{1,3}\.){3}[0-9]{1,3}$"
          required
        />
      </div>

     <!-- NUEVO: selector de MIBs -->
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

      <!-- Input de OIDs: ahora siempre visible -->
      <div class="form-group">
        <label for="oids">OID(s)</label>
        <input
          id="oids"
          type="text"
          v-model="oids"
          placeholder="Por ejemplo: 1.3.6.1.2.1.1.1.0 o 1.3.6.1.2.1.2.2.1.10"
        />
      </div> 

      <!-- Botón -->
      <button type="submit" :disabled="loading">
        {{ loading ? 'Consultando...' : 'Enviar SNMP Get' }}
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
  name: 'SnmpGet',
  data() {
    return {
     user: '',
      ip: '',
      commonMibs: [
        { name: 'MIB-2',   oid: '1.3.6.1.2.1' },
        { name: 'IF-MIB',  oid: '1.3.6.1.2.1.2' },
        { name: 'UCDAVIS',   oid: '1.3.6.1.4.1.2021' },
        // …otras MIBs comunes
      ],
      selectedMib: '',
      oids: '',
      loading: false,
      result: null,
      error: null,
    }
  },
    watch: {
        selectedMib(newOid) {
            if (newOid) {
                this.oids = newOid
            }
        }
    },
  methods: {
    async doSnmpGet() {
        this.loading = true
        this.error = null
        this.result = null

        try {
            const res = await axios.get(`${API_URL}/snmp/get`, {
                params: {
                    user: this.user,
                    ip: this.ip,
                    oid: this.oids,
                }
            });

            this.result = JSON.stringify(res.data.snmp_result, null, 2);
        } catch (err) {
            // si el servidor devuelve { message: '…' }
            this.error = err.response?.data?.message || err.message
        } finally {
            this.loading = false
        }  
    }
  }
};
</script>

<style scoped>
.snmp-get {
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

