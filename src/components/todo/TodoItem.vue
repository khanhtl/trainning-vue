<template>
    <div class="todo-item">
        <div class="btn btn-check" @click="handleChecked" :class="{ 'editting': todo.isEdit }">
            <div v-if="!todo.isCompleted" class="btn-check-circle"></div>
            <span v-else><i class="fa-solid fa-circle-check"></i></span>
        </div>
        <div ref="todoContent" class="todo-content" @dblclick="handleDbClick">
            <div v-if="!todo.isEdit" class="todo-text" :class="{ 'completed': todo.isCompleted }">
                {{ todo.text }}</div>
            <input ref="input" @blur="todo.isEdit = false" @keydown.enter="todo.isEdit = false" v-model="todo.text" v-else
                class="todo-input" />
        </div>
        <div @click="handleRemove" v-if="!todo.isEdit" class="btn btn-remove"><i class="fa-solid fa-xmark"></i></div>
    </div>
</template>

<script>
export default {
    props: {
        todo: {
            type: Object,
            default: {
            }
        }
    },
    methods: {
        handleChecked() {
            this.$emit('click-check', this.todo.id)
        },
        handleRemove() {
            this.$emit('click-remove', this.todo.id)
        },
        handleDbClick(e) {
            this.todo.isEdit = true;
            setTimeout(() => {
                let input = this.$refs.input;
                input && input.focus();
            });
        }
    },

}
</script>

<style lang="scss" scoped>
.todo-item {
    display: flex;
    height: 56px;
    background: #fff;
    border-top: 1px solid #eee;
    box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
    align-items: center;
    padding: 0 8px;

    &:hover {
        .btn-remove {
            display: flex !important;
        }
    }

    .btn {
        cursor: pointer;
        width: 24px;
        height: 24px;
        display: flex;
        justify-content: center;
        align-items: center;

        &.btn-check {
            &.editting {
                opacity: 0;
            }

            .fa-circle-check {
                :deep(path) {
                    fill: green;
                }

            }

            .btn-check-circle {
                width: 16px;
                height: 16px;
                border-radius: 50%;
                border: 1px solid #ccc;
            }
        }

        &.btn-remove {
            display: none;
            opacity: 0.5;

            &:hover {
                opacity: 1;
            }

            .fa-xmark {

                :deep(path) {
                    fill: red;
                }

            }
        }
    }

    .todo-content {
        font-size: 28px;
        flex: 1;
        height: 100%;
        display: flex;
        align-items: center;

        .todo-text {
            padding: 0 8px;
            user-select: none;

            &.completed {
                color: #ccc;
                text-decoration: line-through;
            }
        }

        .todo-input {
            height: 100%;
            border: none;
            font-size: 28px;
            flex: 1;
            padding: 0 8px;
            outline: none;
            box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
        }
    }
}
</style>