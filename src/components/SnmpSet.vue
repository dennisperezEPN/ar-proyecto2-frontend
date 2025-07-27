<!-- src/components/SnmpSet.vue -->
<template>
  <div class="snmp-set">
    <h2>SNMP Set</h2>

    <form @submit.prevent="doSnmpSet">
      <!-- Usuario -->
      <div class="form-group">
        <label for="user">Usuario SNMPv3</label>
        <input id="user" v-model="user" type="text" placeholder="snmpuser" required />
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

      <!-- Nivel de seguridad -->
      <div class="form-group">
        <label for="securityLevel">Nivel de seguridad (SNMPv3)</label>
        <select id="securityLevel" v-model="securityLevel" required>
          <option value="noAuthNoPriv">noAuthNoPriv</option>
          <option value="authNoPriv">authNoPriv</option>
          <option value="authPriv">authPriv</option>
        </select>
      </div>

      <!-- Autenticación -->
      <template v-if="securityLevel !== 'noAuthNoPriv'">
        <div class="form-group">
          <label for="authKey">Clave de autenticación</label>
          <input
            id="authKey"
            v-model="authKey"
            type="password"
            placeholder="Auth pass"
            required
          />
        </div>
        <div class="form-group">
          <label for="authAlg">Algoritmo de autenticación</label>
          <select id="authAlg" v-model="authAlg" required>
            <option value="MD5">MD5 (HMAC‑MD5)</option>
            <option value="SHA">SHA (HMAC‑SHA)</option>
          </select>
        </div>
      </template>

      <!-- Privacidad -->
      <template v-if="securityLevel === 'authPriv'">
        <div class="form-group">
          <label for="privKey">Clave de privacidad</label>
          <input
            id="privKey"
            v-model="privKey"
            type="password"
            placeholder="Priv pass"
            required
          />
        </div>
        <div class="form-group">
          <label for="privAlg">Algoritmo de privacidad</label>
          <select id="privAlg" v-model="privAlg" required>
            <option value="DES">DES</option>
            <option value="AES">AES</option>
          </select>
        </div>
      </template>

      <!-- Selector de MIB común -->
      <div class="form-group">
        <label for="mibSelect">MIB común</label>
        <select id="mibSelect" v-model="selectedMib">
          <option value="">— Ninguna —</option>
          <option v-for="mib in commonMibs" :key="mib.name" :value="mib.oid">
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
          required
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
      <button type="submit" :disabled="loading" class="btn">
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

const API_URL = "http://localhost:8000";

export default {
  name: 'SnmpSet',
  data() {
    return {
      user: '',
      ip: '',
      commonMibs: [
        { name: 'MIB-2',   oid: '1.3.6.1.2.1' },
        { name: 'IF-MIB',  oid: '1.3.6.1.2.1.2' },
        { name: 'UCDAVIS', oid: '1.3.6.1.4.1.2021' },
      ],
      selectedMib: '',
      oid: '',
      type: 'OctetString',
      value: '',
      securityLevel: 'noAuthNoPriv',
      authKey: '',
      authAlg: 'MD5',
      privKey: '',
      privAlg: 'DES',
      loading: false,
      result: null,
      error: null,
    };
  },
  watch: {
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

      const payload = {
        user: this.user,
        ip: this.ip,
        oid: this.oid,
        value: this.value,
        type: this.type,
        security_level: this.securityLevel
      };
      if (this.securityLevel !== 'noAuthNoPriv') {
        payload.auth_key = this.authKey;
        payload.auth_protocol = this.authAlg;
      }
      if (this.securityLevel === 'authPriv') {
        payload.priv_key = this.privKey;
        payload.priv_protocol = this.privAlg;
      }

      try {
        const res = await axios.post(`${API_URL}/snmp/set`, payload);
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
  max-width: 600px;
  margin: 2rem auto;
  padding: 1.5rem;
  background: #2e2e2e;           /* Fondo oscuro para la “carta” */
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.7);
}

.snmp-set h2 {
  color: #fff;
  margin-bottom: 1rem;
}

.snmp-set label {
  color: #ccc;
  display: block;
  margin-bottom: 0.5rem;
  font-weight: 600;
}

/* Inputs y selects oscuros */
input,
select {
  width: 100%;
  padding: 0.6rem 0.8rem;
  font-size: 1rem;
  background: #3a3a3a;           /* Gris oscuro para inputs */
  color: #eee;
  border: 1px solid #555;
  border-radius: 4px;
  transition: border-color .2s, box-shadow .2s;
}

input::placeholder {
  color: #888;
}

input:focus,
select:focus {
  outline: none;
  border-color: #4caf50;         /* verde de acento */
  box-shadow: 0 0 0 3px rgba(76,175,80,0.2);
}

/* Botón principal idéntico */
button {
  width: 100%;
  padding: 0.75rem;
  font-size: 1rem;
  background: #43a047;           /* verde vivo */
  color: #fff;
  border: none;
  border-radius: 4px;
  font-weight: 600;
  cursor: pointer;
  transition: background .2s, box-shadow .2s;
}
button:not(:disabled):hover {
  background: #357a38;           /* verde más oscuro */
  box-shadow: 0 2px 6px rgba(0,0,0,0.4);
}
button:disabled {
  background: #7aa67a;           /* disabled */
  cursor: not-allowed;
}

/* Resultado y error */
.result pre {
  background: transparent;
  color: #bada55;               /* tono lima */
  font-family: monospace;
  white-space: pre-wrap;
  word-wrap: break-word;
}

.error {
  margin-top: 0.5rem;
  color: #ff6b6b;               /* rojo suave */
  font-size: 0.9rem;
  font-weight: 600;
}
</style>

