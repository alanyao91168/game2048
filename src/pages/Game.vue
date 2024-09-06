<template>
  <q-page class="flex flex-center">
    <div class="game-container">
      <h2 class="text-h4 q-mb-md">2048</h2>
      <div class="score q-mb-md">分数: {{ score }}</div>
      <div
        class="grid"
        @keydown.prevent="handleKeydown"
        tabindex="0"
        ref="grid"
      >
        <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="row">
          <div
            v-for="(cell, cellIndex) in row"
            :key="cellIndex"
            class="cell"
            :class="'cell-' + cell"
          >
            {{ cell !== 0 ? cell : '' }}
          </div>
        </div>
      </div>
      <q-btn color="primary" label="新游戏" @click="initGame" class="q-mt-md" />
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'PageGame',
  data() {
    return {
      grid: [],
      score: 0,
    };
  },
  mounted() {
    this.initGame();
    this.$refs.grid.focus();
  },
  methods: {
    initGame() {
      this.grid = Array(4)
        .fill()
        .map(() => Array(4).fill(0));
      this.score = 0;
      this.addNewTile();
      this.addNewTile();
    },
    addNewTile() {
      const emptyCells = [];
      for (let i = 0; i < 4; i++) {
        for (let j = 0; j < 4; j++) {
          if (this.grid[i][j] === 0) {
            emptyCells.push({ i, j });
          }
        }
      }
      if (emptyCells.length > 0) {
        const { i, j } =
          emptyCells[Math.floor(Math.random() * emptyCells.length)];
        this.grid[i][j] = Math.random() < 0.9 ? 2 : 4;
      }
    },
    handleKeydown(e) {
      let moved = false;
      switch (e.key) {
        case 'ArrowUp':
          moved = this.move('up');
          break;
        case 'ArrowDown':
          moved = this.move('down');
          break;
        case 'ArrowLeft':
          moved = this.move('left');
          break;
        case 'ArrowRight':
          moved = this.move('right');
          break;
      }
      if (moved) {
        this.addNewTile();
        if (this.isGameOver()) {
          alert('游戏结束!');
        }
      }
    },
    move(direction) {
      let moved = false;
      const newGrid = JSON.parse(JSON.stringify(this.grid));

      const rotateGrid = (grid) => {
        return grid[0].map((_, index) =>
          grid.map((row) => row[index]).reverse()
        );
      };

      if (direction === 'right') {
        newGrid.forEach(
          (row) => (moved = this.mergeRow(row.reverse()) || moved)
        );
        newGrid.forEach((row) => row.reverse());
      } else if (direction === 'left') {
        newGrid.forEach((row) => (moved = this.mergeRow(row) || moved));
      } else if (direction === 'up') {
        // 修改这里
        let rotatedGrid = rotateGrid(newGrid);
        rotatedGrid.forEach(
          (row) => (moved = this.mergeRow(row.reverse()) || moved)
        );
        rotatedGrid.forEach((row) => row.reverse());
        newGrid.splice(
          0,
          newGrid.length,
          ...rotateGrid(rotateGrid(rotateGrid(rotatedGrid)))
        );
      } else if (direction === 'down') {
        // 修改这里
        let rotatedGrid = rotateGrid(newGrid);
        rotatedGrid.forEach((row) => (moved = this.mergeRow(row) || moved));
        newGrid.splice(
          0,
          newGrid.length,
          ...rotateGrid(rotateGrid(rotateGrid(rotatedGrid)))
        );
      }

      if (moved) {
        this.grid = newGrid;
      }
      return moved;
    },
    mergeRow(row) {
      let moved = false;
      let newRow = row.filter((cell) => cell !== 0);
      for (let i = 0; i < newRow.length - 1; i++) {
        if (newRow[i] === newRow[i + 1]) {
          newRow[i] *= 2;
          this.score += newRow[i];
          newRow.splice(i + 1, 1);
          moved = true;
        }
      }
      while (newRow.length < 4) {
        newRow.push(0);
        moved = true;
      }
      for (let i = 0; i < 4; i++) {
        if (row[i] !== newRow[i]) {
          moved = true;
          break;
        }
      }
      row.splice(0, 4, ...newRow);
      return moved;
    },
    isGameOver() {
      for (let i = 0; i < 4; i++) {
        for (let j = 0; j < 4; j++) {
          if (this.grid[i][j] === 0) return false;
          if (i < 3 && this.grid[i][j] === this.grid[i + 1][j]) return false;
          if (j < 3 && this.grid[i][j] === this.grid[i][j + 1]) return false;
        }
      }
      return true;
    },
  },
});
</script>

<style scoped>
.game-container {
  text-align: center;
}
.grid {
  display: inline-block;
  background-color: #bbada0;
  border-radius: 6px;
  padding: 15px;
  outline: none;
}
.row {
  display: flex;
}
.cell {
  width: 100px;
  height: 100px;
  margin: 5px;
  border-radius: 3px;
  background-color: #ccc0b3;
  color: #776e65;
  font-size: 36px;
  font-weight: bold;
  display: flex;
  justify-content: center;
  align-items: center;
}
.cell-2 {
  background-color: #eee4da;
}
.cell-4 {
  background-color: #ede0c8;
}
.cell-8 {
  background-color: #f2b179;
  color: #f9f6f2;
}
.cell-16 {
  background-color: #f59563;
  color: #f9f6f2;
}
.cell-32 {
  background-color: #f67c5f;
  color: #f9f6f2;
}
.cell-64 {
  background-color: #f65e3b;
  color: #f9f6f2;
}
.cell-128 {
  background-color: #edcf72;
  color: #f9f6f2;
}
.cell-256 {
  background-color: #edcc61;
  color: #f9f6f2;
}
.cell-512 {
  background-color: #edc850;
  color: #f9f6f2;
}
.cell-1024 {
  background-color: #edc53f;
  color: #f9f6f2;
}
.cell-2048 {
  background-color: #edc22e;
  color: #f9f6f2;
}
</style>
