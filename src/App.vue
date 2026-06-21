<script setup>
  import { ref } from 'vue';
  import inicio from './components/inicio.vue';
  import buscar from './components/buscar.vue';
  import CrearNotas from './components/crearNotas.vue';

  const pestanaActiva = ref('inicio');
</script>

<template>
  <div class="fondo-gris-externo">
    
    <div class="contenedor-centrado-app">
      
    <nav class="navbar">
      <div class="nav-logo">
        <div class="logo-container">
          📝 <span class="app-name">TaskApp</span>
          <span class="industry-tag">| industria.ORL</span>
        </div>
      </div>
      <div class="nav-menu">
        <button :class="['nav-link', { activo: pestanaActiva === 'inicio' }]" @click="pestanaActiva = 'inicio'">🏠 Inicio</button>
        <button :class="['nav-link', { activo: pestanaActiva === 'crear' }]" @click="pestanaActiva = 'crear'">➕ Crear Nota</button>
        <button :class="['nav-link', { activo: pestanaActiva === 'buscar' }]" @click="pestanaActiva = 'buscar'">🔍 Buscar / Editar</button>
      </div>
    </nav>

      <main class="contenido-principal">
        <div class="vista-componente">
          <inicio v-if="pestanaActiva === 'inicio'" />
          <CrearNotas v-if="pestanaActiva === 'crear'" />
          <buscar v-if="pestanaActiva === 'buscar'" />
        </div>
      </main>

      <footer class="app-footer">
        <p>&copy; 2026 TaskApp - Gestiona tus tareas de forma inteligente</p>
      </footer>

    </div>
  </div>
</template>

<style>
/* Estilos globales: El fondo de la web será gris claro */
body {
  margin: 0;
  padding: 0;
  background-color: #cbd5e1; /* Color de las franjas laterales (Gris Slate 300) */
  font-family: 'Segoe UI', Roboto, sans-serif;
}
</style>

<style scoped>
/* Pantalla completa externa */
.fondo-gris-externo {
  background-color: #e2e8f0; /* Puedes cambiar este color para las franjas de los lados */
  min-height: 100vh;
  width: 100%;
  display: flex;
  justify-content: center; /* Centra el bloque hijo horizontalmente */
  padding: 0;
}

/* LA CLAVE: El bloque blanco centrado de la App */
.contenedor-centrado-app {
  width: 100%;
  max-width: 1050px; /* Aquí controlas qué tan ancha quieres tu app en el centro */
  background-color: #f8fafc; /* Color de fondo de TU aplicación */
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  /* Sombra sutil a los lados para que se despegue de las franjas laterales */
  box-shadow: 0 0 40px rgba(0, 0, 0, 0.08); 
}

/* Barra de Navegación */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #ffffff;
  padding: 20px 40px;
  border-bottom: 1px solid #e2e8f0;
}

.nav-logo {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 1.3rem;
  font-weight: 700;
  color: #1e293b;
}

.nav-logo span {
  background: linear-gradient(135deg, #4f46e5, #6366f1);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.nav-menu {
  display: flex;
  gap: 12px;
}

/* Botones de Navegación */
.nav-link {
  background: none;
  border: none;
  padding: 10px 18px;
  font-size: 0.95rem;
  font-weight: 600;
  color: #64748b;
  cursor: pointer;
  border-radius: 8px;
  transition: all 0.2s ease;
}

.nav-link:hover {
  color: #4f46e5;
  background-color: #f1f5f9;
}

.nav-link.activo {
  color: #ffffff;
  background-color: #4f46e5;
  box-shadow: 0 4px 12px rgba(79, 70, 229, 0.15);
}

/* Contenedor del Contenido */
.contenido-principal {
  flex: 1;
  padding: 40px;
  background-color: #f8fafc; /* Color interno de las pestañas */
}

.vista-componente {
  animation: fadeIn 0.25s ease-in-out;
}

/* Pie de página */
.app-footer {
  text-align: center;
  padding: 20px;
  background-color: #ffffff;
  border-top: 1px solid #e2e8f0;
  color: #94a3b8;
  font-size: 0.85rem;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(4px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

  @media (max-width: 1050px) {
    .contenedor-centrado-app {
      max-width: 100%;
      min-height: 100vh;
      box-shadow: none;
      /* Eliminamos bordes laterales innecesarios */
      padding: 0; 
    }
    
    .navbar {
      padding: 15px 15px; /* Reducimos el padding lateral para ganar espacio */
    }

    .contenido-principal {
      padding: 15px; /* El contenido se ajusta mejor al ancho de la pantalla */
    }
    
    /* Ajuste para que el texto del logo no se vea diminuto */
    .app-name {
      font-size: 1.1rem;
    }
    
    .industry-tag {
      font-size: 0.8rem;
    }
  }
</style>