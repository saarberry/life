<template>
    <div class="Row" v-for="(row, i) in cellGrid" :key="`row-${i}`">
        <Cell
            v-for="(alive, j) in row"
            :key="`row-${i}-col-${j}`"
            :alive="alive"
        />
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import Cell from "./components/Cell.vue";

export default defineComponent({
    name: "App",
    components: { Cell },
    setup() {
        // A cell is a 20px cube.
        const cellSize = 20;

        // We use nested arrays to keep track of the cell state,
        // with just a boolean to indicate if the cell is alive or not.
        const cellGrid = ref<boolean[][]>([]);

        // Make just enough cells to cover the screen.
        function generateCellsForWindow() {
            const cols = Math.floor(window.innerWidth / cellSize);
            const rows = Math.floor(window.innerHeight / cellSize);

            console.log(cols, rows);

            // Clear the existing cell map.
            cellGrid.value = [];

            // Fill it with new cells.
            for (let currentRow = 0; currentRow < rows; currentRow++) {
                const row: boolean[] = [];
                for (let currentCol = 0; currentCol < cols; currentCol++) {
                    const alive = Math.random() > 0.8;
                    row.push(alive);
                }

                cellGrid.value.push(row);
            }
        }

        generateCellsForWindow();

        return { cellGrid };
    },
});
</script>

<style>
#app {
    @apply flex flex-col items-center justify-center;
    @apply bg-gray-900;
    @apply w-screen h-screen overflow-hidden;
}

.Row {
    @apply flex flex-row items-center;
}
</style>
