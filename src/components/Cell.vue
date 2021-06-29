<template>
    <div
        class="Cell"
        :class="{ 'Cell--alive': alive, 'Cell--clickable': clickable }"
    ></div>
</template>

<script lang="ts">
import { ref, defineComponent } from "vue";
export default defineComponent({
    name: "Cell",
    props: {
        alive: {
            type: Boolean,
            required: true,
        },
        clickable: {
            type: Boolean,
            required: true,
        },
    },
});
</script>

<style scoped>
.Cell {
    @apply w-4 h-4 border-2 rounded m-0.5;
    @apply bg-pink-800 border-pink-600;
    @apply opacity-0;
    @apply transition-opacity;
}

.Cell--alive {
    @apply opacity-100;
}

.Cell--clickable {
    @apply cursor-pointer;
}

.Cell--clickable:hover {
    @apply opacity-25;
}
</style>
