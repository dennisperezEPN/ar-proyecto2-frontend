<template>
  <div class="welcome">
    <h1>Proyecto II - Administración de Redes</h1>
    <p class="tagline">
      Una interfaz sencilla y moderna para gestionar dispositivos SNMPv3
    </p>

    <section class="intro">
      <h2>¿De qué trata este proyecto?</h2>
      <p>
        Este proyecto es una aplicación frontend construida con Vue.js que se conecta a un backend FastAPI
        para realizar operaciones SNMPv3 (GET, GETNEXT, SET y TRAPS) con soporte completo de
        autenticación y privacidad. Su objetivo es simplificar el monitoreo y configuración
        de equipos de red sin tener que escribir directamente scripts en Python o usar CLI.
      </p>
    </section>

    <section class="features">
      <h2>Características principales</h2>
      <ul>
        <li>Operaciones SNMPv3: <strong>GET</strong>, <strong>GETNEXT</strong>, <strong>SET</strong> y <strong>TRAPS</strong>.</li>
        <li>Soporte de niveles de seguridad: <em>noAuthNoPriv</em>, <em>authNoPriv</em> y <em>authPriv</em>.</li>
        <li>Selección de MIBs comunes y edición de OIDs manual.</li>
        <li>Validación de formularios y feedback en tiempo real.</li>
      </ul>
    </section>

    <section class="getting-started">
      <h2>Cómo empezar</h2>
      <ol>
        <li>Clona el repositorio:<br /><code>git clone https://github.com/tu-usuario/snmp-manager-ui.git</code></li>
        <li>Instala dependencias:<br /><code>npm install</code> o <code>yarn install</code></li>
        <li>Levanta el servidor backend:<br /><code>uvicorn main:app --reload</code></li>
        <li>Arranca la interfaz:<br /><code>npm run serve</code> o <code>yarn serve</code></li>
        <li>Abre <code>http://localhost:8080</code> en tu navegador</li>
      </ol>
    </section>

    <section class="links">
      <h2>Enlaces útiles</h2>
      <ul>
        <li><a href="https://github.com/tu-usuario/snmp-manager-ui" target="_blank">Repositorio en GitHub</a></li>
        <li><a href="https://pysnmp.readthedocs.io/" target="_blank">Documentación de PySNMP</a></li>
        <li><a href="https://fastapi.tiangolo.com/" target="_blank">FastAPI</a></li>
      </ul>
    </section>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
};
</script>

<style scoped>
.welcome {
  max-width: 700px;
  margin: 2rem auto;
  padding: 1.5rem;
  background: var(--bg-panel);
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.6);
}

.welcome h1 {
  margin-bottom: 0.5rem;
  color: var(--accent-green);
  text-align: center;
}

.welcome .tagline {
  text-align: center;
  color: var(--text-light);
  margin-bottom: 1.5rem;
  font-style: italic;
}

.welcome section {
  margin-bottom: 1.5rem;
}

.welcome h2 {
  color: var(--accent-green);
  margin-bottom: 0.75rem;
}

.welcome p {
  color: var(--text-light);
  line-height: 1.6;
}

.welcome ul, .welcome ol {
  padding-left: 1.25rem;
  color: var(--text-light);
}

.welcome ul li, .welcome ol li {
  margin-bottom: 0.5rem;
}

.welcome code {
  background: #343434;
  color: #bada55;
  padding: 0.2rem 0.4rem;
  border-radius: 4px;
  font-family: monospace;
}

.welcome a {
  color: var(--accent-green);
  text-decoration: none;
}

.welcome a:hover {
  color: var(--hover-green);
  text-decoration: underline;
}
</style>

