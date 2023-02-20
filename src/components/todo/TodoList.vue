<template>
    <div class="todo">
        <h1 class="todo-title">Todos</h1>
        <div class="todo-header">
            <div @click="handleAllCompleted" class="icon-left" :class="{ 'all-completed': isAllCompleted }">
                <i class="fa-solid fa-chevron-down"></i>
            </div>
            <input ref="input" placeholder="What need to be dones?" v-model="todoText" @keyup.enter="handleAddtodo"
                class="add-todo" />
        </div>
        <div class="todo-items">
            <TodoItem @click-remove="handleClickRemove" @click-check="handleClickCheck" v-for="(todo, index) in filterTodo"
                :todo="todo" />
        </div>
        <div v-if="todos?.length" class="todo-footer">
            <div class="footer-left">{{ activeTodos?.length }} item left</div>
            <div class="footer-center">

                <div v-for="{ status, text, cls } in footerBtn" :class="[{ 'active': status == activeStatus }, cls]"
                    @click="handleFilter(status)" class="footer-btn">{{ text }}</div>
            </div>
            <div @click="handleClearCompleted" v-if="completedTodos?.length" class="footer-right">Clear Completed</div>
        </div>
    </div>
</template>

<script>
import TodoItem from './TodoItem.vue';

export default {
    data() {
        return {
            todoText: "",
            todos: [],
            activeStatus: 1,
            footerBtn: [
                {
                    cls: 'btn-all',
                    text: 'All',
                    status: 1
                },
                {
                    cls: 'btn-active',
                    text: 'Active',
                    status: 2
                },
                {
                    cls: 'btn-completed',
                    text: 'Completed',
                    status: 3
                },
            ]
        };
    },
    mounted() {
        this.todos = this.loadTodos();
        this.$refs.input?.focus();
    },
    methods: {
        handleClickRemove(id) {
            this.todos = this.todos.filter(todo => todo.id != id);
            if (!this.todos?.length) this.$refs.input?.focus();
        },
        handleClickCheck(id) {
            let todo = this.todos.find(todo => todo.id == id)
            todo && (todo.isCompleted = !todo.isCompleted)
        },
        handleClearCompleted() {
            this.todos = this.activeTodos;
            this.$refs.input?.focus();
        },
        handleFilter(status) {
            this.activeStatus = status;
        },
        handleAddtodo() {
            if (!this.todoText?.trim()) return;
            this.todos.push({
                id: new Date().getTime(),
                text: this.todoText,
                isCompleted: false,
                isEdit: false
            });
            this.todoText = '';
            this.$refs.input?.focus();
        },
        handleAllCompleted(e) {
            this.todos = this.todos?.map(todo => {
                return {
                    ...todo,
                    isCompleted: !this.isAllCompleted
                }
            })
        },
        saveTodos() {
            localStorage.setItem('app-todos', JSON.stringify(this.todos));
        },
        loadTodos() {
            return JSON.parse(localStorage.getItem('app-todos')) || []
        }
    },
    computed: {
        activeTodos() {
            return this.todos.filter(x => !x.isCompleted)
        },
        completedTodos() {
            return this.todos.filter(x => x.isCompleted)
        },
        filterTodo() {
            switch (this.activeStatus) {
                case 2:
                    return this.activeTodos;
                case 3:
                    return this.completedTodos;
                default:
                    return this.todos.slice(0);
            }
        },
        isAllCompleted() {
            return this.todos?.length && this.completedTodos?.length === this.todos?.length;
        }
    },
    watch: {
        todos: {
            deep: true,
            handler(val) {
                this.saveTodos();
            }
        }
    },
    components: { TodoItem }
}
</script>

<style lang="scss" scoped> .todo {
     width: 550px;

     .todo-title {
         display: flex;
         justify-content: center;
         align-items: center;
         font-size: 80px;
         margin: 16px 0;
     }

     .todo-header {
         display: flex;
         align-items: center;
         height: 56px;
         padding: 0 8px;
         box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;

         .icon-left {
             width: 24px;
             height: 24px;
             display: flex;
             align-items: center;
             justify-content: center;
             cursor: pointer;

             &.all-completed {
                 opacity: 1;
             }

             opacity: 0.5;
         }

         .add-todo {
             flex: 1;
             height: 100%;
             font-size: 28px;
             margin-left: 16px;
             border: none;
             outline: none;
         }
     }

     .todo-footer {
         height: 56px;
         border-top: 1px solid #eee;
         box-shadow: 0 1px 1px rgb(0 0 0 / 20%),
             0 8px 0 -3px #f6f6f6,
             0 9px 1px -3px rgb(0 0 0 / 20%),
             0 16px 0 -6px #f6f6f6,
             0 17px 2px -6px rgb(0 0 0 / 20%);
         background: #fff;
         display: flex;
         align-items: center;
         font-size: 14px;
         padding: 0 8px;

         .footer-center {
             display: flex;
             flex: 1;
             justify-content: center;

             .footer-btn {
                 cursor: pointer;
                 border: 1px solid transparent;
                 padding: 4px;

                 &.active {
                     border: 1px solid #888;
                 }

                 &:hover {
                     border: 1px solid #888;
                 }
             }

             .btn-active {
                 margin: 0 16px;
             }
         }

         .footer-right {
             &:hover {
                 text-decoration: underline;
                 cursor: pointer;
             }
         }
     }
 }
</style>