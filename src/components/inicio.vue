<script setup>
    import api from '@/api';
    import axios from 'axios';
    import { ref } from 'vue';

    // Creación de arreglo vacío
    const tareas = ref([]); 

    // Creamos la función para obtener las tareas
    async function obtenerTareas() {
        try {
            const todasTareas = await api.get("/tareas");
            tareas.value = todasTareas.data;
        } catch (error) {
            console.error("Error al obtener las tareas:", error);
            alert("No se pudieron cargar las tareas. Verifica que el servidor esté encendido.");
        }
    }
</script>

<template>
    <div class="contenedor-lista">
        <header class="encabezado">
            <h1>Lista Completa de Tareas</h1>
            <p class="subtitulo">Visualiza y administra el estado de todas tus actividades de un vistazo</p>
            <button @click="obtenerTareas" class="btn-cargar">
                🔄 Mostrar todas mis tareas
            </button>
        </header>

        <div v-if="tareas.length === 0" class="lista-vacia">
            <p>No hay tareas cargadas visualmente. Presiona el botón para consultar.</p>
        </div>

        <div v-else class="cuadrilla-tareas">
            <div v-for="tarea in tareas" :key="tarea.id" class="tarjeta-tarea">
                <div class="tarjeta-header">
                    <span class="tarea-id">#{{ tarea.id }}</span>
                    <span :class="['badge-estado', tarea.estado.replace(' ', '-')]">
                        {{ tarea.estado }}
                    </span>
                </div>
                
                <h3 class="tarea-titulo">{{ tarea.titulo }}</h3>
                <p class="tarea-descripcion">
                    {{ tarea.descripcion || 'Sin descripción disponible.' }}
                </p>
            </div>
        </div>
    </div>
</template>

<style scoped>
/* Contenedor Principal */
.contenedor-lista {
    max-width: 1000px;
    margin: 0 auto;
    padding: 40px 20px;
    font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
    background-color: #f8fafc;
    min-height: 100vh;
}

/* Encabezado */
.encabezado {
    text-align: center;
    margin-bottom: 40px;
}

.encabezado h1 {
    color: #1e293b;
    font-size: 2.2rem;
    margin-bottom: 10px;
}

.subtitulo {
    color: #64748b;
    font-size: 1.1rem;
    margin-bottom: 24px;
}

/* Botón Principal */
.btn-cargar {
    padding: 14px 28px;
    background-color: #4f46e5;
    color: #ffffff;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    box-shadow: 0 4px 12px rgba(79, 70, 229, 0.2);
    transition: all 0.2s ease;
}

.btn-cargar:hover {
    background-color: #4338ca;
    transform: translateY(-2px);
    box-shadow: 0 6px 16px rgba(79, 70, 229, 0.3);
}

.btn-cargar:active {
    transform: translateY(0);
}

/* Estado de Lista Vacía */
.lista-vacia {
    text-align: center;
    padding: 40px;
    background-color: #ffffff;
    border: 2px dashed #cbd5e1;
    border-radius: 12px;
    color: #94a3b8;
    font-size: 1.05rem;
}

/* Grid / Cuadrícula de Tarjetas */
.cuadrilla-tareas {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 20px;
}

/* Tarjeta Individual */
.tarjeta-tarea {
    background-color: #ffffff;
    border: 1px solid #e2e8f0;
    border-radius: 12px;
    padding: 20px;
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
    display: flex;
    flex-direction: column;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.tarjeta-tarea:hover {
    transform: translateY(-4px);
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.08);
}

/* Cabecera de la Tarjeta (ID y Estado) */
.tarjeta-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 14px;
}

.tarea-id {
    font-size: 0.85rem;
    color: #94a3b8;
    font-weight: bold;
}

/* Badges / Etiquetas de Estado */
.badge-estado {
    padding: 4px 10px;
    border-radius: 20px;
    font-size: 0.75rem;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    background-color: #cbd5e1;
    color: #475569;
}

/* Colores personalizados automáticos según el estado */
.badge-estado.pendiente {
    background-color: #fef3c7;
    color: #d97706;
}

.badge-estado.en-progreso {
    background-color: #e0f2fe;
    color: #0369a1;
}

.badge-estado.completada {
    background-color: #dcfce7;
    color: #15803d;
}

/* Textos de la Tarea */
.tarea-titulo {
    color: #1e293b;
    margin: 0 0 10px 0;
    font-size: 1.2rem;
    font-weight: 600;
    line-height: 1.3;
}

.tarea-descripcion {
    color: #64748b;
    font-size: 0.95rem;
    line-height: 1.5;
    margin: 0;
    word-break: break-word; /* Evita que textos largos rompan la tarjeta */
}
</style>