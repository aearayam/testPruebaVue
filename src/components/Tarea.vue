<template>
    <div>
        <h1 class="display-4 text-center">Listado de Tareas</h1>
        <hr>
        <div class="row">
            <div class="col-lg-8 offset-lg-2">
                <div class="card mt-4">
                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" v-model="tarea" class="form-control form-control-lg" placeholder="Agregar Tarea">
                            <div class="input-group-append">
                                <button v-on:click="agregarTarea()" class="btn btn-success btn-lg"><i class="fas fa-plus"></i> Agregar</button>
                            </div>
                        </div>
                        <br>
                        <div class="text-center">
                            <div v-if="loading" class="spinner-border text-success" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                        <h5 v-if="listTareas.length == 0">No hay tareas por hacer.</h5>
                        <ul v-if="!loading" class="list-group">
                            <li v-for="(tarea, index) of listTareas" :key="index" class="list-group-item d-flex justify-content-between">
                                <span class="cursor" v-bind:class="{'text-success' : tarea.estado}" v-on:click="editarTarea(tarea, tarea.id)">
                                    <i v-bind:class="[tarea.estado ? 'fas fa-check-circle' : 'far fa-circle']"></i>
                                </span>
                                {{ tarea.nombre }}
                                <span class="text-danger cursor" v-on:click="eliminarTarea(tarea.id)">
                                    <i class="fas fa-trash-alt"></i>
                                </span>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

const URL = "https://backend-prueba-service.azurewebsites.net/api/Tarea/";  //antes: https://localhost:44392/api/Tarea/

    export default {
        name: 'Tarea',
        data() {
            return {
                tarea: '',
                listTareas: [],
                loading: false
            }
        },
        methods: {
            agregarTarea() {
                const tarea = {
                    nombre: this.tarea,
                    estado: false
                }
                //this.listTareas.push(tarea);
                this.loading = true;
                //axios.post("https://localhost:44392/api/Tarea/", tarea).then(response => {
                axios.post(URL, tarea).then(response => {
                    console.log(response);
                    this.loading = false;
                    this.obtenerTareas(); //vuelve a cargar las tareas de la base de datos para mostrarlas
                }).catch(error => {
                    console.error(error);
                    this.loading = false;
                })
                this.tarea = '';
            },
            eliminarTarea(id) {
                //this.listTareas.splice(id, 1)
                //axios.delete("https://localhost:44392/api/Tarea/" + id).then(response => {
                axios.delete(URL + id).then(response => {
                    console.log(response);
                    this.obtenerTareas();
                }).catch(error => {
                    console.log(error);
                }); 
            },
            editarTarea(tarea, id) {
                //this.listTareas[id].estado = !tarea.estado
                this.loading = true;
                //axios.put("https://localhost:44392/api/Tarea/" + id, tarea).then(() => {
                axios.put(URL + id, tarea).then(() => {
                    this.obtenerTareas();
                    this.loading = false;
                }).catch(() => this.loading = false)
            },
            obtenerTareas(){
                this.loading = true;
                //axios.get("https://localhost:44392/api/Tarea").then(response => {
                axios.get(URL).then(response => {
                    console.log(response);
                    this.listTareas = response.data;
                    this.loading = false;
                }).catch(() => this.loading = false)
            }
        },
        created: function(){
            this.obtenerTareas();
        }
    }
</script>

<style scoped>
.cursor{
    cursor: pointer;
}
</style>