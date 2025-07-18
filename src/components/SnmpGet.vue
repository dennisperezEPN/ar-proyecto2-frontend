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

      <!-- Elegir MIB u OID -->
      <div class="form-group">
        <label>Fuente de OID</label>
        <div>
          <label>
            <input
              type="radio"
              value="mib"
              v-model="mode"
            /> MIB
          </label>
          <label>
            <input
              type="radio"
              value="oid"
              v-model="mode"
            /> OID
          </label>
        </div>
      </div>

      <!-- Si elige MIB, mostrar select; si OID, mostrar input -->
      <div class="form-group" v-if="mode==='mib'">
        <label for="mib">Selecciona MIB</label>
        <select id="mib" v-model="mibName" required>
          <option disabled value="">-- elige una MIB --</option>
          <option>sysDescr.0</option>
          <option>sysUpTime.0</option>
          <option>sysContact.0</option>
          <!-- añade más entries según tus MIBs cargadas -->
        </select>
      </div>

      <div class="form-group" v-else>
        <label for="oid">OID</label>
        <input
          id="oid"
          v-model="oid"
          type="text"
          placeholder="1.3.6.1.2.1.1.1.0"
          required
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

export default {
  name: 'SnmpGet',
  data() {
    return {
      user: '',
      ip: '',
      mode: 'mib',      // 'mib' o 'oid'
      mibName: '',
      oid: '',
      loading: false,
      result: null,
      error: null,
    };
  },
  methods: {
    async doSnmpGet() {
      this.loading = true;
      this.error = this.result = null;

      // decide el valor de query param oid según el modo
      const queryOid = this.mode === 'mib'
        ? this.mibName
        : this.oid;

      try {
        const res = await axios.get('http://127.0.0.1:8000/snmp/get', {
          params: {
            user: this.user,
            ip: this.ip,
            oid: queryOid,
          }
        });
        this.result = JSON.stringify(res.data.snmp_result, null, 2);
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

