<template>
    <div class="Row" v-for="(row, y) in cellGrid" :key="`row-${y}`">
        <Cell
            v-for="(alive, x) in row"
            :key="`row-${y}-col-${x}`"
            :alive="alive"
            :clickable="!isRunning"
            @click="toggleCell(x, y)"
        />
    </div>

    <div class="Presets" v-if="presetsVisible">
        <button class="Button" @click="applyFlyerPreset">I want flyers!</button>
    </div>

    <div class="Buttons">
        <button class="Button" @click="togglePresets">Presets</button>

        <button class="Button" @click="start" v-if="!isRunning">Start</button>
        <button class="Button" @click="stop" v-else>Stop</button>
    </div>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from "vue";
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

        /**
         * Make just enough cells to cover the screen.
         * @return {void}
         */
        function generateCellsForWindow(): void {
            const colCount = Math.floor(window.innerWidth / cellSize);
            const rowCount = Math.floor(window.innerHeight / cellSize);

            // Fill the grid with dead cells.
            cellGrid.value = Array.from({ length: rowCount }, () =>
                Array.from({ length: colCount }, () => false)
            );
        }

        // Generate initial cell state.
        generateCellsForWindow();

        /**
         * Iterate for the next state.
         * @return {void}
         */
        function evolve(): void {
            // Go through every cell to see what its new value should be.
            cellGrid.value = cellGrid.value.map((row, x, grid) => {
                return row.map((alive, y) => {
                    // Get a count of living neighbours.
                    let livingNeighbourCount = [
                        grid?.[x - 1]?.[y - 1] || false,
                        grid?.[x - 1]?.[y] || false,
                        grid?.[x - 1]?.[y + 1] || false,

                        grid?.[x]?.[y - 1] || false,
                        grid?.[x]?.[y + 1] || false,

                        grid?.[x + 1]?.[y - 1] || false,
                        grid?.[x + 1]?.[y] || false,
                        grid?.[x + 1]?.[y + 1] || false,
                    ].filter((alive) => alive).length;

                    return (
                        (livingNeighbourCount === 2 && alive) ||
                        livingNeighbourCount === 3
                    );
                });
            });
        }

        // The game runs on a setInterval loop.
        const loop = ref<number | undefined>(undefined);

        // If we're running or not.
        const isRunning = computed(() => loop.value !== undefined);

        /**
         * Start game.
         * @return {void}
         */
        function start(): void {
            loop.value = setInterval(evolve, 150);
        }

        /**
         * Stop game
         * @return {void}
         */
        function stop(): void {
            clearInterval(loop.value);
            loop.value = undefined;
        }

        /**
         * Toggle the cell at the given coordinates.
         * @param {number} col
         * @param {number} row
         * @return {void}
         */
        function toggleCell(col: number, row: number): void {
            if (!isRunning.value) {
                cellGrid.value[row][col] = !cellGrid.value[row][col];
            }
        }

        // If the presets are visible.
        const presetsVisible = ref<boolean>(false);

        /**
         * Toggle the button visibility for the presets.
         * @return {void}
         */
        function togglePresets(): void {
            presetsVisible.value = !presetsVisible.value;
        }

        /**
         * Generates an array with width amount of false values, but
         * every given position being true.
         * @param {number} width
         * @param {number[]} positions
         * @return {boolean[]}
         */
        function patternHelper(
            width: number,
            positions: number[] = []
        ): boolean[] {
            return Array.from({ length: width }, (value, i) =>
                positions.includes(i)
            );
        }

        /**
         * Generate a gosper glider gun.
         * @return {void}
         */
        function applyFlyerPreset(): void {
            const gosperGliderGun = [
                patternHelper(38),
                patternHelper(38, [26]),
                patternHelper(38, [24, 26]),
                patternHelper(38, [14, 15, 22, 23, 36, 37]),
                patternHelper(38, [13, 17, 22, 23, 36, 37]),
                patternHelper(38, [2, 3, 12, 18, 22, 23]),
                patternHelper(38, [2, 3, 12, 16, 18, 19, 24, 26]),
                patternHelper(38, [12, 18, 26]),
                patternHelper(38, [13, 17]),
                patternHelper(38, [14, 15]),
            ];

            cellGrid.value = cellGrid.value.map((row, x) => {
                return row.map((value, y) => {
                    return gosperGliderGun?.[x]?.[y] || false;
                });
            });
        }

        return {
            cellGrid,
            toggleCell,
            start,
            stop,
            isRunning,
            presetsVisible,
            togglePresets,
            applyFlyerPreset,
        };
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

.Buttons {
    @apply absolute;
    @apply top-0 right-0 m-2;
    @apply flex flex-row;
}

.Presets {
    @apply absolute;
    @apply top-10 right-0 m-2;
    @apply flex flex-row;
}

.Button {
    @apply m-1 px-4 py-0.5;
    @apply border-2 rounded-md border-pink-400 bg-pink-200 text-pink-600;
}
</style>
