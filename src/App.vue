<template>
    <div class="container">
        <h2>To-Do List</h2>
        <!-- Form -->
        <TodoSimpleForm @add-todo="addTodo" />

        <div v-if="!todos.length">추가된 Todo가 없습니다.</div>

        <!-- List -->
        <TodoList :todos="todos" @toggle-todo="toggleTodo" />
    </div>
</template>

<script>
import { ref } from "vue";
import TodoSimpleForm from "./components/TodoSimpleForm.vue";
import TodoList from "./components/TodoList.vue";

export default {
    components: {
        TodoSimpleForm,
        TodoList,
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

        const toggleTodo = (index) => {
            // console.log(todos.value[index]); 변경(토글) 전
            todos.value[index].completed = !todos.value[index].completed;
            // console.log(todos.value[index]); 변경(토글) 후
        };

        return {
            todos,
            addTodo,
            deleteTodo,
            toggleTodo,
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
