<template>
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
</template>

<script>
import { ref } from "vue";

export default {
    setup(props, context) {
        const todo = ref("");
        const hasError = ref(false);

        /**
         * Todo 추가
         */
        const onSubmit = () => {
            if (todo.value === "") {
                hasError.value = true;
            } else {
                /** 자식 -> 부모 emit('이벤트 이름') */
                context.emit("add-todo", {
                    id: Date.now(),
                    subject: todo.value,
                    completed: false,
                });
                hasError.value = false;
                todo.value = "";
            }
        };

        return {
            todo,
            hasError,
            onSubmit,
        };
    },
};
</script>

<style></style>
