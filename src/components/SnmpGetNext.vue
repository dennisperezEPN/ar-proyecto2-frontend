<!-- src/components/SnmpGetNext.vue -->
<template>
  <div class="snmp-get-next">
    <h2>SNMP GetNext</h2>

    <form @submit.prevent="doSnmpGetNext">
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

      <!-- Fuente de OID -->
      <div class="form-group">
        <label>Fuente de OID</label>
        <div>
          <label>
            <input type="radio" value="mib" v-model="mode" />
            MIB
          </label>
          <label>
            <input type="radio" value="oid" v-model="mode" />
            OID
          </label>
        </div>
      </div>

      <!-- Selección de MIB -->
      <div class="form-group" v-if="mode === 'mib'">
        <label for="mib">MIB</label>
        <select id="mib" v-model="mibName">
          <option value="SNMPv2-MIB::sysDescr.0">sysDescr</option>
          <option value="SNMPv2-MIB::sysUpTime.0">sysUpTime</option>
          <option value="SNMPv2-MIB::sysContact.0">sysContact</option>
          <option value="SNMPv2-MIB::sysName.0">sysName</option>
        </select>
      </div>

      <!-- Entrada de OID -->
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
        {{ loading ? 'Consultando...' : 'Enviar SNMP GetNext' }}
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
  name: 'SnmpGetNext',
  data() {
    return {
      user: '',
      ip: '',
      mode: 'mib',      // 'mib' o 'oid'
      mibName: 'SNMPv2-MIB::sysDescr.0',
      oid: '',
      loading: false,
      result: null,
      error: null,
    };
  },
  methods: {
    async doSnmpGetNext() {
      this.loading = true;
      this.error = this.result = null;

      const queryOid = this.mode === 'mib'
        ? this.mibName
        : this.oid;

      try {
        const res = await axios.get('http://127.0.0.1:8000/snmp/getnext', {
          params: {
            user: this.user,
            ip: this.ip,
            oid: queryOid,
          }
        });
        this.result = JSON.stringify(res.data.snmp_next_result, null, 2);
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
.snmp-get-next {
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

