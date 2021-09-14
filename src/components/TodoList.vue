<template>
    <div class="todo-container">
        <div class="todo-list">
            <ul>
                <li v-for="todo in todos" :key="todo.id">

                    <input
                            v-if="!todo.completed"
                            class="form-check-input"
                            v-model="todo.completed"
                            type="checkbox">
                    <span
                            v-on:dblclick="editTodo(todo)"
                            v-if="!todo.isEdited"
                            :class="{completed: todo.completed}"
                    >{{todo.text}}</span>

                    <input
                            @keypress.enter="editTodoDone(editTodoId)"
                            v-model="editTodoText"
                            v-if="todo.isEdited"
                            type="text">

                    <button @click="deleteTodo(todo, $event)"
                            type="button"
                            class="btn btn-outline-danger">Delete
                    </button>
                </li>
            </ul>

            <input v-model="todoText"
                   @keypress.enter="addTodo"
                   class="form-control"
                   type="text"
                   placeholder="Add TODO">
        </div>
    </div>

</template>

<script>
    export default {
        props: {
            todos: {
                type: Array,
            }

        },
        data() {
            return {

                newTodo: {
                    id: null,
                    text: '',
                    completed: null,
                    isEdited: null,
                },

                nextTodoId: 0,
                todoText: '',
                editTodoId: null,
                editTodoText: '',
            }
        },
        methods: {
            addTodo() {
                this.newTodo.id = this.nextTodoId++
                this.newTodo.text = this.todoText
                this.newTodo.completed = false
                this.newTodo.isEdited = false
                this.$emit('create', this.newTodo)
                this.newTodo = {
                        id: null,
                        text: '',
                        completed: null,
                        isEdited: null,
                }
                this.resetInputs()
            },
            deleteTodo(todo, event) {
                event.stopPropagation()
                let index = this.todos.indexOf(todo)
                this.todos.splice(index, 1)
            },
            resetInputs() {
                this.todoText = ''
            },
            editTodo(todo) {
                let index = this.todos.indexOf(todo)
                this.todos[index].isEdited = true
                this.editTodoText = this.todos[index].text
                this.editTodoId = index
            },
            editTodoDone(id) {
                this.todos[id].text = this.editTodoText
                this.todos[id].isEdited = false
                this.editTodoId = null
                this.editTodoText = ''
            }
        },
    }
</script>

<style scoped>
    .todo-container {
        padding: 20px;
        background-color: whitesmoke;
        max-width: 700px;
        margin: 0 auto;
    }

    .todo-list {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    ul {
        list-style: none;
        margin: 0;
        padding: 0;
    }

    li {
        margin-bottom: 15px;
        max-width: 400px;
        min-width: 300px;
        padding: 10px;
        background-color: white;
        border-radius: 10px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    li input {
        margin-right: 10px;
    }

    li span {
        margin-right: 10px;
        justify-self: flex-start;
    }

    .completed {
        text-decoration: line-through;
        color: darkgray;
    }

    .form-control {
        max-width: 300px;
    }

    .btn-outline-danger {
        margin-left: auto;
    }
</style>