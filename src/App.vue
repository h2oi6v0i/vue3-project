<template>
    <div class="container">
        <h2>To-Do List</h2>
        <TodoSimpleForm @add-todo="addTodo" />
        <!-- <div v-if="todos.length === 0">추가된 Todo가 없습니다.</div> 이것도 됨 -->
        <div v-if="!todos.length">추가된 Todo가 없습니다.</div>
        <div v-for="(todo, index) in todos" :key="todo.id" class="card mt-2">
            <div class="card-body d-flex align-items-center p-2">
                <div class="form-check flex-grow-1">
                    <input
                        class="form-check-input"
                        type="checkbox"
                        v-model="todo.completed"
                    />
                    <label
                        class="form-check-label"
                        :class="{ 'is-completed': todo.completed }"
                    >
                        {{ todo.subject }}
                    </label>
                </div>
                <div>
                    <button
                        class="btn btn-danger btn-sm"
                        @click="deleteTodo(index)"
                    >
                        Delete
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { ref } from "vue";
import TodoSimpleForm from "./components/TodoSimpleForm.vue";

export default {
    components: {
        TodoSimpleForm,
    },

    setup() {
        const todos = ref([]);

        /**
         * Todo 추가
         * - 인자로 받는 todo는 자식 컴포넌트에서 받아 온 데이터
         */
        const addTodo = (todo) => {
            todos.value.push(todo);
        };

        /**
         * Todo 삭제
         */
        const deleteTodo = (index) => {
            todos.value.splice(index, 1);
        };

        return {
            todos,
            addTodo,
            deleteTodo,
        };
    },
};
</script>

<style>
.is-completed {
    color: gray;
    text-decoration: line-through;
}
</style>
