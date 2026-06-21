<script setup>
    import axios from 'axios';
    import { ref } from 'vue';
    import api from '@/api';

    // Estado para el ID de búsqueda
    const id = ref("");
    
    // Almacena la tarea encontrada (objeto)
    const notaEncontrada = ref(null);
    
    // Bandera para manejar cuando no se encuentra una tarea (Error 404)
    const sinResultados = ref(false);

    // Función para buscar la tarea por ID
    async function buscarTarea() {
        if (!id.value.trim()) {
            alert("Por favor, ingresa un ID");
            return;
        }
        
        try {
            const respuesta = await api.get(`/tareas/${id.value}`);
            notaEncontrada.value = respuesta.data;
            sinResultados.value = false; // Reiniciamos el estado de no encontrado
            mostrarForm.value = false;   // Ocultamos el formulario de edición por si estaba abierto
        } catch (error) {
            console.error(error);
            notaEncontrada.value = null; // Limpiamos resultados anteriores
            
            // Si el backend responde con 404 (No encontrado)
            if (error.response && error.response.status === 404) {
                sinResultados.value = true;
            } else {
                alert("Hubo un error al buscar la tarea");
            }
        }
    }

    // --- Función para Eliminar ---
    async function eliminar() {
        if (!id.value) return;
        
        // Alerta de confirmación antes de borrar (buena práctica de UX)
        const confirmar = confirm("¿Estás seguro de que deseas eliminar esta tarea?");
        if (!confirmar) return;

        try {
            await api.delete(`/tareas/${id.value}`);
            alert("Tarea eliminada con éxito");
            notaEncontrada.value = null; // Limpiamos la pantalla
            id.value = "";               // Limpiamos el buscador
        } catch (error) {
            console.error(error);
            alert("Error al intentar eliminar la tarea");
        }
    }

    // --- Función para Actualizar ---
    const mostrarForm = ref(false);

    // Variables independientes para los campos del formulario de actualización
    const titulo = ref("");
    const descripcion = ref("");
    const estado = ref("");

    // Carga los datos actuales de la tarea en los inputs y muestra el formulario
    function abrirForm() {
        mostrarForm.value = true;
        titulo.value = notaEncontrada.value.titulo;
        descripcion.value = notaEncontrada.value.descripcion;
        estado.value = notaEncontrada.value.estado;
    }

    // Cierra el formulario de edición sin guardar cambios
    function cerrarForm() {
        mostrarForm.value = false;
    }

    // Envía los datos actualizados al backend
    async function actualizar() {
        try {
            await api.put(`/tareas/${id.value}`, {
                titulo: titulo.value,
                descripcion: descripcion.value,
                estado: estado.value
            });
            alert("Tarea actualizada con éxito");
            
            // Refrescamos visualmente la tarjeta con los nuevos datos
            notaEncontrada.value = { 
                id: id.value, 
                titulo: titulo.value, 
                descripcion: descripcion.value, 
                estado: estado.value 
            };
            cerrarForm(); // Ocultamos el formulario
        } catch (error) {
            console.error(error);
            alert("Error al actualizar la tarea");
        }
    }
</script>

<template>
    <div class="contenedor-principal">
        <div class="seccion-busqueda">
            <h1 class="titulo-pagina">Gestor de Tareas por ID</h1>
            <p class="subtitulo">Ingresa el identificador de la tarea para administrarla</p>
            <div class="barra-busqueda">
                <input type="text" placeholder="Ej. 12345" v-model="id" @keyup.enter="buscarTarea" />
                <button @click="buscarTarea" class="btn-primario">Buscar Tarea</button> 
            </div>
        </div>

        <div v-if="sinResultados" class="mensaje-error">
            ⚠️ No se encontró ninguna tarea con el ID <strong>{{ id }}</strong>.
        </div>

        <div v-if="notaEncontrada" class="seccion-resultado">
            <div class="tarjeta-tarea">
                <h2 class="titulo-tarjeta">Tarea Encontrada</h2>
                
                <div class="datos-tarea">
                    <p><strong>ID:</strong> {{ notaEncontrada.id || id }}</p>
                    <p><strong>Título:</strong> {{ notaEncontrada.titulo }}</p>
                    <p><strong>Descripción:</strong> {{ notaEncontrada.descripcion }}</p>
                    <p><strong>Estado:</strong> <span class="badge-estado">{{ notaEncontrada.estado }}</span></p>
                </div>

                <div class="botones-accion">
                    <button @click="abrirForm" class="btn-secundario">Actualizar</button>
                    <button @click="eliminar" class="btn-peligro">Eliminar</button>
                </div>

                <div v-if="mostrarForm" class="overlay-formulario">
                    <div class="formulario-edicion">
                        <h3>Editar Detalles</h3>
                        <input type="text" placeholder="Título" v-model="titulo" />
                        <input type="text" placeholder="Descripción" v-model="descripcion" />
                        <input type="text" placeholder="Estado" v-model="estado" />
                        
                        <div class="botones-formulario">
                            <button @click="cerrarForm" class="btn-secundario">Cancelar</button>
                            <button @click="actualizar" class="btn-primario">Guardar Cambios</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
/* Contenedor Principal */
.contenedor-principal {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 40px 20px;
    font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
    background-color: #f8fafc;
    min-height: 90vh;
}

/* Sección de Búsqueda */
.seccion-busqueda {
    text-align: center;
    margin-bottom: 30px;
    width: 100%;
    max-width: 500px;
}

.titulo-pagina {
    color: #1e293b;
    margin-bottom: 8px;
    font-size: 1.8rem;
}

.subtitulo {
    color: #64748b;
    font-size: 1rem;
    margin-bottom: 20px;
}

.barra-busqueda {
    display: flex;
    gap: 10px;
    width: 100%;
}

.barra-busqueda input {
    flex: 1;
    padding: 12px 16px;
    border: 2px solid #e2e8f0;
    border-radius: 8px;
    font-size: 1rem;
    outline: none;
    transition: border-color 0.3s ease;
}

.barra-busqueda input:focus {
    border-color: #4f46e5;
}

/* Mensajes de Error/Alerta */
.mensaje-error {
    padding: 15px 20px;
    background-color: #fee2e2;
    border: 1px solid #f87171;
    color: #b91c1c;
    border-radius: 8px;
    font-weight: 500;
    margin-bottom: 20px;
    width: 100%;
    max-width: 500px;
}

/* Tarjeta de Resultado */
.seccion-resultado {
    width: 100%;
    max-width: 500px;
}

.tarjeta-tarea {
    background-color: #ffffff;
    border: 1px solid #e2e8f0;
    border-radius: 12px;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.05);
    padding: 30px;
    position: relative;
}

.titulo-tarjeta {
    margin-top: 0;
    color: #1e293b;
    font-size: 1.3rem;
    margin-bottom: 20px;
    border-bottom: 1px solid #f1f5f9;
    padding-bottom: 10px;
}

.datos-tarea p {
    color: #475569;
    font-size: 1rem;
    margin: 12px 0;
    line-height: 1.5;
}

.badge-estado {
    background-color: #e0f2fe;
    color: #0369a1;
    padding: 4px 8px;
    border-radius: 6px;
    font-size: 0.85rem;
    font-weight: 600;
    text-transform: uppercase;
}

/* Botones Base */
button {
    cursor: pointer;
    border: none;
    border-radius: 8px;
    font-size: 0.95rem;
    font-weight: 600;
    transition: all 0.2s ease;
}

.botones-accion {
    display: flex;
    gap: 12px;
    margin-top: 25px;
}

/* Clases de Botones Metódicos */
.btn-primario {
    padding: 12px 20px;
    background-color: #4f46e5;
    color: #ffffff;
}

.btn-primario:hover {
    background-color: #3f35ca;
}

.btn-secundario {
    flex: 1;
    padding: 12px;
    background-color: #f1f5f9;
    color: #334155;
}

.btn-secundario:hover {
    background-color: #e2e8f0;
}

.btn-peligro {
    flex: 1;
    padding: 12px;
    background-color: #ef4444;
    color: #ffffff;
}

.btn-peligro:hover {
    background-color: #dc2626;
}

/* Formulario de Edición Desplegable */
.overlay-formulario {
    margin-top: 25px;
    padding-top: 20px;
    border-top: 2px dashed #e2e8f0;
}

.formulario-edicion {
    display: flex;
    flex-direction: column;
    gap: 12px;
}

.formulario-edicion h3 {
    color: #1e293b;
    margin: 0 0 8px 0;
    font-size: 1.1rem;
}

.formulario-edicion input {
    padding: 10px 12px;
    border: 1px solid #cbd5e1;
    border-radius: 6px;
    font-size: 0.95rem;
    outline: none;
}

.formulario-edicion input:focus {
    border-color: #4f46e5;
}

.botones-formulario {
    display: flex;
    gap: 10px;
    margin-top: 8px;
}
</style>