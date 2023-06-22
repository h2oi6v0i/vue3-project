<template>
    <div class="container">
        <h2>To-Do List</h2>
        <form @submit.prevent="onSubmit">
            <div class="d-flex">
                <div class="flex-grow-1 mr-2">
                    <input
                        class="form-control"
                        type="text"
                        v-model="todo"
                        placeholder="Type new to-do"
                    />
                </div>
                <div>
                    <button type="submit" class="btn btn-primary">Add</button>
                </div>
            </div>
            <!-- 에러 메시지는 자주 사용되기 때문에 v-if 보다 v-show가 적절함 (렌더링) -->
            <div v-show="hasError" style="color: red">
                This field cannot be empty
            </div>
        </form>
        <div v-for="todo in todos" :key="todo.id" class="card mt-2">
            <div class="card-body p-2">
                {{ todo.subject }}
            </div>
        </div>
    </div>
</template>

<script>
import { ref } from "vue";

export default {
    setup() {
        const todo = ref("");
        const todos = ref([
            { id: 1, subject: "휴대폰 사기" },
            { id: 2, subject: "장보기" },
        ]);
        const hasError = ref(false);

        const onSubmit = () => {
            if (todo.value === "") {
                hasError.value = true;
            } else {
                todos.value.push({
                    id: Date.now(),
                    subject: todo.value,
                });
                hasError.value = false;
            }

            todo.value = "";
        };

        return {
            todo,
            todos,
            onSubmit,
            hasError,
        };
    },
};
</script>

<style></style>
