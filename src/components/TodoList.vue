<template>
  <div>
    <input type="text" class="todo-input" placeholder="What need to be done" v-model="newTodo"
           @keyup.enter="addTodo">
    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
    <div v-for="(todo, index) in todosFilterd" :key="todo.id" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed">
        <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label"
             :class="{ completed : todo.completed }">
          {{ todo.title }}
        </div>
        <input v-else type="text" class="todo-item-edit" v-model="todo.title" @blur="doneEdit(todo)"
               @keyup.enter="doneEdit(todo)" v-focus @keyup.esc="cancelEdit(todo)">
      </div>
      <div class="remove-item" @click="removeTodo(index)">
        &times;
      </div>
    </div>
    </transition-group>
    <div class="extra-container">
      <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">Check All</label>
      </div>
      <div>{{ remaining }} items left</div>
    </div>
    <div class="extra-container">
      <div>
        <button :class="{ active: filter === 'all' }"
                @click="filter = 'all'">All
        </button>
        <button :class="{ active: filter === 'active' }"
                @click="filter = 'active'">Active
        </button>
        <button :class="{ active: filter === 'completed' }"
                @click="filter = 'completed'">Completed
        </button>
      </div>

      <transition name="fade">
        <button v-if="showClearCmpletedBTN" @click="clearComplete">
          Clear completed
        </button>
      </transition>

    </div>
  </div>
</template>

<script>
    export default {
        name: "TodoList",

        data() {
            return {
                newTodo: '',
                beforeEditCache: "",
                filter: "all",
                count: 3,
                todos: [
                    {
                        'id': 1,
                        'title': 'Finish Vue T',
                        'completed': false,
                        'editing': false,
                    },
                    {
                        'id': 2,
                        'title': 'Job',
                        'completed': false,
                        'editing': false,
                    },

                ]
            }
        },
        computed: {
            remaining() {
                return this.todos.filter(todo => !todo.completed).length;
            },
            anyRemaining() {
                return this.remaining !== 0;
            },
            todosFilterd() {
                if (this.filter === "active") {
                    return this.todos.filter(todo => !todo.completed);
                } else if (this.filter === "completed") {
                    return this.todos.filter(todo => todo.completed);
                }

                return this.todos;
            },
            showClearCmpletedBTN() {
                return this.todos.filter(todo => todo.completed).length > 0;
            }
        },
        directives: {
            focus: {
                inserted: function (el) {
                    el.focus()
                }
            }
        },

        methods: {
            addTodo() {
                if (this.newTodo.trim().length === 0) {
                    return
                }
                this.todos.push({
                    id: this.count,
                    title: this.newTodo,
                    completed: false,
                    editing: false
                });

                this.count++;
                this.newTodo = '';
            },
            editTodo: function (todo) {
                this.beforeEditCache = todo.title;
                todo.editing = true;
            },
            cancelEdit: function (todo) {
                todo.title = this.beforeEditCache;
                todo.editing = false;
            },
            doneEdit: function (todo) {
                if (todo.title.trim().length === 0) {
                    todo.title = this.beforeEditCache;
                }
                todo.editing = false;
            },
            removeTodo(index) {
                this.todos.splice(index, 1);
            },
            checkAllTodos() {
                this.todos.forEach((todo) => todo.completed = event.target.checked);
            },
            clearComplete() {
                this.todos = this.todos.filter(todo => !todo.completed);
            }

        }
    }
</script>

<style lang="scss">
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.css");
  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
    text-align: center;

    &:focus {
      outline: 0;
    }
  }

  .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 600ms;
  }

  .remove-item {
    cursor: pointer;
    margin-left: 14px;

    &:hover {
      color: black;
    }
  }

  .todo-item-left {
    display: flex;
    align-items: center;
  }

  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }

  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    &:focus {
      outline: none;
    }
  }

  .completed {
    text-decoration: line-through;
    color: gray;
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-top: 14px;
  }

  button {
    font-size: 14px;
    background-color: white;
    appearance: none;

    &:hover {
      background-color: lightgreen;
    }

    &:focus {
      outline: none;
    }
  }

  .active {
    background: lightgreen;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity 1s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }

</style>
