<template>
  <canvas ref="game" :height="size" :width="size"></canvas>
</template>

<script>
const ALIVE = 1;
const DEAD = 0;

export default {
  name: 'Game',
  props: {
    size: Number,
  },
  data() {
    return {
      canvas: null,
      ctx: null,
      numberOfCellsInRow: 30,
      framesPerSecond: 5,
    }
  },
  mounted() {
    const canvas = this.$refs.game;
    const ctx = canvas.getContext('2d');
    const seeds = this.seeding();
    this.evolve(ctx, seeds);
  },
  methods: {
    seeding() {
      let grid = new Array(this.numberOfCellsInRow);

      for (let r = 0; r < grid.length; r++) {
        grid[r] = new Array(this.numberOfCellsInRow);
        for (let c = 0; c < grid[r].length; c++) {
          grid[r][c] = Math.random() > 0.75 ? ALIVE : DEAD;
        }
      }

      return grid;
    },
    evolve(ctx, grid) {
      ctx.clearRect(0, 0, this.size, this.size);
      this.drawGrid(ctx, grid);

      const gridNext = this.getNextGeneration(grid);

      setTimeout(() => {
        requestAnimationFrame(() => {
          this.evolve(ctx, gridNext);
        });
      }, 1000 / this.framesPerSecond);
    },
    getNextGeneration(grid) {
      let newGrid = new Array(this.numberOfCellsInRow);

      for (let r = 0; r < newGrid.length; r++) {
        newGrid[r] = new Array(this.numberOfCellsInRow);
        for (let c = 0; c < newGrid[r].length; c++) {
          const status = grid[r][c];
          const aliveNeighbors = this.countAliveNeighbors(grid, r, c);

          // Apply Conway's rule
          if (aliveNeighbors < 2 || aliveNeighbors > 3) {
            newGrid[r][c] = DEAD;
          } else if (aliveNeighbors === 3) {
            newGrid[r][c] = ALIVE;
          } else {
            newGrid[r][c] = status;
          }
        }
      }

      return newGrid;
    },
    countAliveNeighbors(grid, r, c) {
      let sum = 0;
      const maxRow = grid.length;
      const maxCol = grid[0].length;
      for (let i = -1; i < 2; i++) {
        for (let j = -1; j < 2; j++) {
          // Connect head to tail, like sphere
          const row = (r + i + maxRow) % maxRow;
          const col = (c + j + maxCol) % maxCol;
          sum += grid[row][col];
        }
      }
      sum -= grid[r][c]; // Remove middle cell, we only care about neighbors
      return sum;
    },
    drawGrid(ctx, grid) {
      const cellSize = this.size / this.numberOfCellsInRow;
      ctx.strokeStyle = '#cccccc';

      for (let i = 0; i < grid.length; i++) {
        for (let j = 0; j < grid.length; j++) {
          if (grid[i][j]) {
            ctx.fillRect(
              i * cellSize,
              j * cellSize,
              cellSize,
              cellSize,
            );
          }
          ctx.strokeRect(
            i * cellSize,
            j * cellSize,
            cellSize,
            cellSize,
          );
        }
      }
    },
  },
}
</script>

<style scoped>
canvas {
  display: block;
  margin: 0 auto;
}
</style>
