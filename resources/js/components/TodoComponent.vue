<template>
    <div class="w-25">
        <form @submit.prevent="saveData">
            <div class="input-group mb-3 w-100">
                <input v-model="form.title" :class="{'is-invalid' : this.form.errors.has('title')}" type="text" class="form-control form-control-lg" aria-label="Recipient's username" aria-describedby="button-addon2" @keydown="form.errors.clear('title')">
                <div class="input-group-append">
                     <button class="btn btn-success" type="submit" id="button-addon2">Add this to your list</button>
                </div>
            </div>
            <span class="text-danger pt-3 pb-3" style="font-size: 20px;" v-if="form.errors.has('title')" v-text="form.errors.get('title')"></span>
        </form>
        <section class="pb-5 header text-center">
            <div class="container mx-auto border-0 shadow">
                <div class="row">
                    <div v-for="todo in todos" :key="todo.id" class="w-100 d-flex align-items-center p-3 bg-white border-bottom">
                        <span class="mr-2">
                            <button v-on:click="toggleTodo(todo)" v-if="todo.completed == false" class="btn btn-primary btn-sm" type="button" title="Add"><i class="fa fa-circle-thin "></i></button>
                            <button v-on:click="toggleTodo(todo)" v-if="todo.completed == true" class="btn btn-primary btn-sm" type="button" title="Add"><i class="fa fa-check-circle-o"></i></button>
                        </span>
                        <div class="font-weight-bolder">
                            <span v-if="editmode == false || editmode != todo.id">{{todo.title}}</span><input v-if="editmode == todo.id" v-model="todo.title" type="text">
                        </div>

                        <div class="mx-auto d-flex align-items-center">
                            <span>
                                <button v-on:click="editmode = todo.id"  v-if="editmode != todo.id" class="btn btn-warning btn-sm ml-1" type="button" data-toggle="tooltip" data-placement="top" title="Edit"><i class="fa fa-edit"></i></button>
                                <button v-on:click="updateTodo(todo)" v-if="editmode == todo.id" class="btn btn-warning btn-sm ml-1" type="button" data-toggle="tooltip" data-placement="top" title="Edit"><i class="fa fa-check-square-o"></i></button>
                            </span>
                            <span>
                                <button v-on:click="deleteTodo(todo)" class="btn btn-danger btn-sm ml-1" type="button" data-toggle="tooltip" data-placement="top" title="Edit"><i class="fa fa-trash"></i></button>
                            </span>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                editmode: false,
                todos: '',
                form: new Form({
                    title: '',
                })
            }
        },
        methods:{
            deleteTodo(e){
              let data = new FormData();
              data.append('_method', 'DELETE')
                axios.post('/api/todo/'+e.id, data).then((res) => {
                    this.todos = res.data
                }).catch((error) => {
                    this.form.errors.record(error.response.data.errors)
                })
            },
            updateTodo(e){
                this.editmode = false
                let data = new FormData();
                data.append('_method', 'PATCH')
                data.append('title', e.title)
                axios.post('/api/todo/'+e.id, data).then((res) => {})
                    .catch((error) => {
                    this.form.errors.record(error.response.data.errors)
                })
            },
            toggleTodo(e){
                e.completed = !e.completed
                let data = new FormData();
                data.append('_method', 'PATCH')
                if(e.completed == true){
                    data.append('completed',1);
                }
                if(e.completed == false){
                    data.append('completed', 0)
                }
                axios.post('/api/todo/'+e.id, data)
            },
            getTodos(){
              axios.get('/api/todo').then((res) => {
                  this.todos = res.data
              }).catch((error) => {
                  console.log(error)
              })
            },
            saveData(){
                let data = new FormData();
                data.append('title',this.form.title)
                axios.post('/api/todo',data).then((res) =>{
                    this.form.reset()
                    this.getTodos()
                }).catch((error) => {
                    this.form.errors.record(error.response.data.errors)
                })
            }
        },
        mounted() {
            this.getTodos()
        }
    }
</script>
