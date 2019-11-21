<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="completed" @change="doneEdit">
      <div v-if="!editing" @dblclick="editTodo" class="todo-item-label"
           :class="{ completed : todo.completed }">
        {{ title }}
      </div>
      <input v-else type="text" class="todo-item-edit" v-model="title" @blur="doneEdit"
             @keyup.enter="doneEdit" v-focus @keyup.esc="cancelEdit">
    </div>
    <div class="remove-item" @click="removeTodo(index)">
      &times;
    </div>
  </div>
</template>

<script>
    export default {
        name: "todo-item",
        watch: {
            checkAll() {
                // if(this.checkAll){
                //     this.completed = true;
                // }else{
                //     this.completed = this.todo.completed;
                // }

                this.completed = this.checkAll ? true : this.todo.completed;
            }
        },
        directives: {
            focus: {
                inserted: function (el) {
                    el.focus()
                }
            }
        },
        props: {
            todo: {
                type: Object,
                required: true
            },
            index: {
                type: Number,
                required: true
            },
            checkAll: {
                type: Boolean,
                required: true
            }

        },
        data() {
            return {
                'id': this.todo.id,
                'title': this.todo.title,
                'completed': this.todo.completed,
                'editing': this.todo.editing,
                'beforeEditCache': '',
            }
        },
        methods: {
            removeTodo(index) {
                eventBus.$emit('removedTodo', index);
            },
            editTodo: function () {
                this.beforeEditCache = this.title;
                this.editing = true;
            },

            cancelEdit: function () {
                this.title = this.beforeEditCache;
                this.editing = false;

            },
            doneEdit: function () {
                if (this.title.trim().length === 0) {
                    this.title = this.beforeEditCache;
                }
                this.editing = false;
                eventBus.$emit('fineshEdit', {
                    'index': this.index,
                    'todo': {
                        'id': this.id,
                        'title': this.title,
                        'completed': this.completed,
                        'editing': this.editing
                    }
                });
            },
        }
    }
</script>

<style scoped>

</style>
