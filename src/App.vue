<template>
  <div id="app">
    <section>
      <table class="table">
        <tr v-for="(row, rowIndex) in table" :key="rowIndex">
          <td
              v-for="(cell, colIndex) in row"
              :key="colIndex"
              :class="cell === 0 ? 'water' : 'land'"
          >
            {{ cell }}
          </td>
        </tr>
      </table>

      <table class="table">
        <tr v-for="(row, rowIndex) in floodedTable" :key="rowIndex">
          <td
              v-for="(cell, colIndex) in row"
              :key="colIndex"
              :class="cell === 0 ? 'water' : 'land'"
          >
            {{ cell }}
          </td>
        </tr>
      </table>
    </section>
  </div>
</template>

<script>
export default {

  data() {
    return {
      table: this.generateRandomTable(),
    };
  },

  computed: {
    floodedTable() {
      const tableCopy = structuredClone(this.table);
      const visited = Array.from({length: 20}, () => Array(20).fill(false));

      const checkLand = (x, y) => {
        const stack = [[x, y]];
        const islandCoordinates = [];

        while (stack.length > 0) {
          const [currentX, currentY] = stack.pop();

          if (visited[currentX][currentY]) {
            continue
          }

          visited[currentX][currentY] = true;
          islandCoordinates.push([currentX, currentY]);

          const nears = [
            [currentX - 1, currentY],
            [currentX + 1, currentY],
            [currentX, currentY - 1],
            [currentX, currentY + 1],
          ];

          nears.forEach(([nx, ny]) => {
            if (nx >= 0 && nx < 20 && ny >= 0 && ny < 20) {
              if (!visited[nx][ny] && tableCopy[nx][ny] === 1) {
                stack.push([nx, ny]);
              }
            }
          })
        }

        if (islandCoordinates.length < 4) {
          islandCoordinates.forEach(([i, j])=>{
            tableCopy[i][j] = 0;
          })
        }
      };

      for (let i = 0; i < 20; i++) {
        for (let j = 0; j < 20; j++) {
          if (tableCopy[i][j] === 1 && !visited[i][j]) {
            checkLand(i, j);
          }
        }
      }

      return tableCopy;
    },
  },
  methods: {
    generateRandomTable() {
      const table = [];
      for (let i = 0; i < 20; i++) {
        const row = [];
        for (let j = 0; j < 20; j++) {
          row.push(Math.random() < 0.5 ? 0 : 1);
        }
        table.push(row);
      }
      return table;
    },
  },
};
</script>

<style scoped>

section {
  display: flex;
  justify-content: space-between;
}

.table {
  border-collapse: collapse;
  margin: 20px 0;
}

.table td {
  width: 30px;
  height: 30px;
  text-align: center;
  font-size: 12px;
  border: 1px solid #ddd;
}

.water {
  background-color: deepskyblue;
}

.land {
  background-color: brown;
}
</style>
