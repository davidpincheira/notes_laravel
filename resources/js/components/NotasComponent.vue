<template>
    <div>
        <form @submit.prevent="editarNota(nota)" v-if="editarActivo">
            <h3>Editar Notas</h3>
            <input type="text" placeholder="Nombre "
            class="form-control mb-2" v-model="nota.nombre">
            <input type="text" placeholder="Descripción "
            class="form-control mb-2" v-model="nota.descripcion">
            <button class="btn btn-success" type="submit">Guardar</button>
            <button class="btn btn-danger" type="submit"  @click="cancelarEdicion">Cancelar</button>
        </form>
        
        <form @submit.prevent="agregar" v-else>
            <h3>Agregar Notas</h3>
            <input type="text" placeholder="Nombre "
            class="form-control mb-2" v-model="nota.nombre">
            <input type="text" placeholder="Descripción "
            class="form-control mb-2" v-model="nota.descripcion">
            <button class="btn btn-primary" type="submit">Agregar</button>
        </form>

        <hr class="mt-3">
        <h3>LISTADO DE NOTAS</h3>
        <ul class="list-group my-2" v-for="(nota, index) in notas" :key="index" >
            <li class="list-group-item-info" >
                <span class="badge badge-primary float-right">
                    {{ nota.updated_at }}
                </span>
                <p> {{ nota.nombre }} </p> 
                <p> {{ nota.descripcion }} </p>
                <button class="btn btn-danger btn-sm" @click="eliminarNota(nota, index)">Eliminar</button>
                <button class="btn btn-info btn-sm" @click="editarFormulario(nota)">Editar</button>
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                notas: [],
                nota: {nombre: '', descripcion:''},
                editarActivo:false
            }
        },
        created(){
            this.llamarDatos();
        },
        methods: {
            agregar(){
                //Validar campos vacios
                if(this.nota.nombre.trim() === '' || this.nota.descripcion.trim()=== ''){
                    alert('Debes completar todos los campos antes de guardar.');
                    return ;
                }
                const notaNueva = this.nota;
                this.nota = {nombre: '', descripcion: ''};    
                axios.post('/notas', notaNueva)
                    .then((res) =>{
                      const notaServidor = res.data;
                      this.notas.push(notaServidor);
                })
            },
            eliminarNota(nota, index){
                axios.delete('/notas/'+nota.id).then(() => {
                    this.notas.splice(index,1)
                })
            },
            editarFormulario(nota){
                this.editarActivo = true;
                this.nota.nombre = nota.nombre;
                this.nota.descripcion = nota.descripcion;
                this.nota.id = nota.id;
            },
            editarNota(nota){
                const params = {nombre: nota.nombre, descripcion:nota.descripcion };
                axios.put('/notas/'+nota.id, params).then(res => {
                    this.editarActivo = false;
                    this.nota = {nombre: '',descripcion:''}

                    this.llamarDatos();

                });
            },
            cancelarEdicion(){
              this.editarActivo = false;
              this.nota = {nombre: '', descripcion: ''};
            },
            llamarDatos(){
                axios.get('/notas').then(res => {
                        this.notas = res.data;
                    })
            }
        },
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>
