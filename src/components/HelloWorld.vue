<template>
    <h1>{{ msg }}</h1>

    <button type="button" @click="count++">count is: {{ count }}</button>
</template>

<script lang="ts">
import { ref, defineComponent } from "vue";
export default defineComponent({
    name: "HelloWorld",
    props: {
        msg: {
            type: String,
            required: true,
        },
    },
    setup: () => {
        const count = ref(0);
        return { count };
    },
});
</script>

<style scoped>
a {
    @apply text-green-500;
}

label {
    @apply mx-2 font-bold;
}

code {
    @apply bg-gray-100;
    @apply px-2 py-1 rounded;
    @apply text-gray-700;
}

button {
    @apply my-4 px-2 py-1 rounded;
    @apply bg-blue-400;
}
</style>
