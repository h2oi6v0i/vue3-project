<template>
    <div :class="nameClass">{{ name }}</div>
    <input :type="type" :value="name" />
    <button class="btn btn-primary" @click="updateName">Click</button>
</template>

<script>
import { ref } from "vue";

export default {
    setup() {
        const name = ref("Gary");
        const type = ref("text");
        const nameClass = ref("");

        const updateName = () => {
            name.value = "Jeon Gary";
            nameClass.value = "name";
        };

        return {
            name,
            type,
            nameClass,
            updateName,
        };
    },
};
</script>

<style>
.name {
    color: cornflowerblue;
    font-weight: 700;
}
</style>
