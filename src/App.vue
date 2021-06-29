<template>
    <div class="Row" v-for="(row, y) in cellGrid" :key="`row-${y}`">
        <Cell
            v-for="(alive, x) in row"
            :key="`row-${y}-col-${x}`"
            :alive="alive"
            @click="onClick(x, y)"
        />
    </div>

    <button class="Start" @click="start">Start</button>
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
            const colCount = Math.floor(window.innerWidth / cellSize);
            const rowCount = Math.floor(window.innerHeight / cellSize);

            // Clear the existing cell map.
            let rows: boolean[][] = [];

            // Fill it with new cells.
            for (let currentRow = 0; currentRow < rowCount; currentRow++) {
                let row: boolean[] = [];
                for (let currentCol = 0; currentCol < colCount; currentCol++) {
                    const alive = Math.random() > 0.9;
                    row.push(false);
                }

                rows.push(row);
            }

            cellGrid.value = rows;
        }

        // Generate initial cell state.
        generateCellsForWindow();

        // Loop at a set interval to play the game.
        function start() {
            const loop = setInterval(() => {
                // Copy the cell grid as a reference so we know what
                // state we're evolving from.
                let previousCellGrid = cellGrid.value.map((row) => [...row]);

                // Modify the actual cellGrid.
                cellGrid.value = cellGrid.value.map((row, x) => {
                    return row.map((alive, y) => {
                        // Get all the neighbours.
                        let neighbours = [
                            previousCellGrid?.[x - 1]?.[y - 1] || false,
                            previousCellGrid?.[x - 1]?.[y] || false,
                            previousCellGrid?.[x - 1]?.[y + 1] || false,

                            previousCellGrid?.[x]?.[y - 1] || false,
                            previousCellGrid?.[x]?.[y + 1] || false,

                            previousCellGrid?.[x + 1]?.[y - 1] || false,
                            previousCellGrid?.[x + 1]?.[y] || false,
                            previousCellGrid?.[x + 1]?.[y + 1] || false,
                        ];

                        let livingNeighbours = neighbours.filter(
                            (alive) => alive === true
                        ).length;

                        if (alive) {
                            return (
                                livingNeighbours > 1 && livingNeighbours <= 3
                            );
                        }

                        return livingNeighbours === 3;
                    });
                });
            }, 150);
        }

        function onClick(col: number, row: number) {
            cellGrid.value[row][col] = !cellGrid.value[row][col];
        }

        return { cellGrid, onClick, start };
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

.Start {
    @apply absolute;
    @apply top-0 right-0 m-1 px-4 py-0.5;
    @apply border-2 rounded border-pink-400 bg-pink-200 text-pink-600;
}
</style>
