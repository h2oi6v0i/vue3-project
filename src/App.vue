<template>
    <div class="container">
        <h2>To-Do List</h2>
        <!-- Search -->
        <input
            class="form-control"
            type="text"
            v-model="searchText"
            placeholder="Search"
        />
        <hr />
        <!-- Form -->
        <TodoSimpleForm @add-todo="addTodo" />
        <div style="color: red">{{ error }}</div>

        <div v-if="!filteredTodos.length">There is nothing to display!</div>

        <!-- List -->
        <TodoList
            :todos="filteredTodos"
            @toggle-todo="toggleTodo"
            @delete-todo="deleteTodo"
        />
    </div>
</template>

<script>
import { ref, computed } from "vue";
import axios from "axios";
import TodoSimpleForm from "./components/TodoSimpleForm.vue";
import TodoList from "./components/TodoList.vue";

export default {
    components: {
        TodoSimpleForm,
        TodoList,
    },

    setup() {
        const todos = ref([]);
        const searchText = ref("");
        const error = ref("");

        /**
         * 검색 필터링 기능 (여기서 todo는 기존 데이터 아니고 임의로 작명)
         */
        const filteredTodos = computed(() => {
            if (searchText.value) {
                return todos.value.filter((todo) => {
                    return todo.subject.includes(searchText.value);
                });
            }

            return todos.value;
        });

        /**
         * DB에 있는 데이터 가져오기
         */
        const getTodos = async () => {
            try {
                const res = await axios.get("http://localhost:3000/todos");
                todos.value = res.data;
            } catch (err) {
                console.log(err);
                error.value = "Something went wrong.";
            }
        };

        getTodos();

        /**
         * Todo 추가
         * - 인자로 받는 todo는 자식 컴포넌트에서 받아 온 데이터
         * - 데이터베이스에 todo 저장(HTTP 요청)한 후 push
         */
        const addTodo = async (todo) => {
            error.value = "";

            try {
                const res = await axios.post("http://localhost:3000/todos", {
                    subject: todo.subject,
                    completed: todo.completed,
                });
                todos.value.push(res.data);
            } catch (err) {
                console.log(err);
                error.value = "Something went wrong.";
            }
        };

        /**
         * Todo 삭제
         * - 자식 컴포넌트에서 보내준 index를 이용하여 해당 함수 실행
         */
        const deleteTodo = (index) => {
            todos.value.splice(index, 1);
        };

        /**
         * Todo 완료/미완료
         */
        const toggleTodo = (index) => {
            todos.value[index].completed = !todos.value[index].completed;
        };

        return {
            todos,
            searchText,
            filteredTodos,
            error,
            getTodos,
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
