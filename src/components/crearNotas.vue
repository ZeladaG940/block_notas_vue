<script setup>
    import { ref } from 'vue';
    import axios from 'axios';
    import api from '@/api';

    const titulo = ref("");
    const descripcion = ref("");
    const estado = ref("pendiente");

    // Creamos la función de crear nota
    async function crearNota() {
        if (!titulo.value.trim()) {
            alert("Por favor, ingresa un título");
            return;
        }
        
        try {
            await api.post("/tareas", {
                titulo: titulo.value,
                descripcion: descripcion.value,
                estado: estado.value
            });
            alert("Nota creada exitosamente");
            // Limpiar formulario tras guardar
            titulo.value = "";
            descripcion.value = "";
            estado.value = "pendiente";
        } catch (error) {
            console.error(error);
            alert("Hubo un error al guardar la nota");
        }
    }
</script>

<template>
    <div class="contenedor-formulario">
        <form @submit.prevent="crearNota" class="tarjeta-nota">
            <h2>Crear Nueva Nota</h2>
            
            <div class="grupo-control">
                <label for="titulo">Título</label>
                <input type="text" id="titulo" name="titulo" placeholder="Ej. Comprar la cena" v-model="titulo" required>
            </div>
            
            <div class="grupo-control">
                <label for="descripcion">Descripción</label>
                <textarea id="descripcion" placeholder="Detalles de la tarea..." v-model="descripcion" rows="4"></textarea>
            </div>
            
            <div class="grupo-control">
                <label for="estado">Estado</label>
                <select id="estado" v-model="estado">
                    <option value="pendiente">⏳ Pendiente</option>
                    <option value="en progreso">⚡ En progreso</option>
                    <option value="completada">✅ Completada</option>
                </select>
            </div>
            
            <button type="submit" class="btn-guardar">Guardar Nota</button>
        </form>
    </div>
</template>

<style scoped>
/* Contenedor principal centrado */
.contenedor-formulario {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 80vh;
    font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
    background-color: #f4f6f9;
    padding: 20px;
}

/* Tarjeta del formulario */
.tarjeta-nota {
    background: #ffffff;
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 8px 24px rgba(149, 157, 165, 0.2);
    width: 100%;
    max-width: 450px;
}

.tarjeta-nota h2 {
    margin-top: 0;
    margin-bottom: 24px;
    color: #2c3e50;
    font-size: 1.5rem;
    text-align: center;
}

/* Grupos de inputs */
.grupo-control {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
}

.grupo-control label {
    margin-bottom: 8px;
    font-weight: 600;
    color: #4a5568;
    font-size: 0.9rem;
}

/* Estilos comunes para inputs, textarea y select */
.grupo-control input,
.grupo-control textarea,
.grupo-control select {
    padding: 12px;
    border: 1.5px solid #cbd5e1;
    border-radius: 8px;
    font-size: 1rem;
    color: #334155;
    background-color: #fff;
    transition: all 0.3s ease;
    outline: none;
}

/* Efecto Focus al hacer clic */
.grupo-control input:focus,
.grupo-control textarea:focus,
.grupo-control select:focus {
    border-color: #4f46e5;
    box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.15);
}

/* Textarea específico */
.grupo-control textarea {
    resize: vertical;
}

/* Botón de guardar */
.btn-guardar {
    width: 100%;
    padding: 14px;
    background-color: #4f46e5;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.2s ease, transform 0.1s ease;
    margin-top: 10px;
}

.btn-guardar:hover {
    background-color: #4338ca;
}

.btn-guardar:active {
    transform: scale(0.98);
}
</style>