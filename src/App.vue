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

        <hr />

        <!-- Pagination -->
        <nav aria-label="Page navigation example">
            <ul class="pagination justify-content-center">
                <li v-if="currentPage !== 1" class="page-item">
                    <a
                        style="cursor: pointer"
                        class="page-link"
                        @click="getTodos(currentPage - 1)"
                    >
                        Previous
                    </a>
                </li>
                <li
                    v-for="page in numberOfPages"
                    :key="page"
                    class="page-item"
                    :class="{ active: currentPage === page }"
                >
                    <a
                        style="cursor: pointer"
                        class="page-link"
                        @click="getTodos(page)"
                        >{{ page }}</a
                    >
                </li>
                <li v-if="currentPage !== numberOfPages" class="page-item">
                    <a
                        style="cursor: pointer"
                        class="page-link"
                        @click="getTodos(currentPage + 1)"
                    >
                        Next
                    </a>
                </li>
            </ul>
        </nav>
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
        const numberOfTodos = ref(0);
        const limit = 5;
        const currentPage = ref(1);

        /**
         * 전체 페이지 계산
         */
        const numberOfPages = computed(() => {
            return Math.ceil(numberOfTodos.value / limit);
        });

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
         * DB에 있는 데이터 가져오기 w/ pagination
         */
        const getTodos = async (page = currentPage.value) => {
            currentPage.value = page;

            try {
                const res = await axios.get(
                    `http://localhost:3000/todos?_page=${page}&_limit=${limit}`
                );
                numberOfTodos.value = res.headers["x-total-count"];
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
         * - DB로 삭제 요청 보낸 후, DB에서 삭제되었을 때 todos 배열에서 삭제하기
         */
        const deleteTodo = async (index) => {
            error.value = "";
            const id = todos.value[index].id;

            try {
                await axios.delete(`http://localhost:3000/todos/${id}`);
                todos.value.splice(index, 1);
            } catch (err) {
                console.log(err);
                error.value = "Something went wrong.";
            }
        };

        /**
         * Todo 완료/미완료
         */
        const toggleTodo = async (index) => {
            error.value = "";
            const id = todos.value[index].id;

            try {
                await axios.patch(`http://localhost:3000/todos/${id}`, {
                    completed: !todos.value[index].completed,
                });
                todos.value[index].completed = !todos.value[index].completed;
            } catch (err) {
                console.log(err);
                error.value = "Something went wrong.";
            }
        };

        return {
            todos,
            searchText,
            error,
            numberOfTodos,
            limit,
            currentPage,
            getTodos,
            addTodo,
            deleteTodo,
            toggleTodo,
            filteredTodos,
            numberOfPages,
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
