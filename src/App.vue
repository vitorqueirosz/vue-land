<template>
  <div class="inputsContainer">
    <Input 
      name="rows" 
      type="text" 
      placeholder="Rows" 
      v-model="display.columns"
      label="Rows"
    />

    <Input 
      name="columns" 
      type="text" 
      placeholder="Columns" 
      v-model="display.rows"
      label="Columns"
    />
  </div>

  <h4>Lands amount: {{ landAmount }}</h4>

  <div 
    class="landsContainer"
    :style="{
      gridTemplateRows: `repeat(${display.columns}, 80px)`,
      gridTemplateColumns: `repeat(${display.rows}, 80px)`, 
     }"
  >
  <div 
    v-for="(row, rIndex) in lands" 
    :key="rIndex + 'row'"
  >
    <div v-for="(column, cIndex) in row" :key="rIndex + cIndex + 'column'">
      <button 
        class="land"
        type="button"
        :style="{ 
          backgroundColor: column ? '#EB942F': '#2FEBE0',
          borderColor: column ? '#D88627': '#25C7BD',
        }"
        @click="() => handleSelectLand([rIndex, cIndex])"
      />
    </div>
  </div>
  </div>
 
</template>

<script>
import { ref, watch } from "vue";
import Input from '@/components/InputC.vue';

export default {
  name: 'App',
  components: {
    Input,
  },
  setup() {
    const amount = ref(1);
    const display = ref({ rows: 5, columns: 4 });
    const lands = ref([
      [1,0,0,1], 
      [0,0,0,0], 
      [0,0,0,0], 
      [1,0,0,0],
      [1,0,0,1], 
    ]);

    const handleLands = (lands) => {
      let landAmount = 0;

      const checkLands = (lands, cords, host) => {
        const [row, column] = cords;
        const hasItem = lands[row]?.[column];

        const TOP = [-1, 0];
        const RIGHT = [0, 1];
        const BOTTOM = [1, 0];
        const LEFT = [0, -1];

        let hasBreak = false;
        lands[row][column] = 2;

        if (hasItem === 2) return;


        if (!hasItem) return landAmount;


        while (!hasBreak) {
          const DIRECTIONS = [RIGHT, BOTTOM, LEFT, TOP];

          DIRECTIONS.forEach(([r, c]) => {
            const currentRow = row + r;
            const currentCol =  column + c;
            const coords = [currentRow, currentCol];
            const hasValue = lands[currentRow]?.[currentCol];

            if (hasValue) checkLands(lands, coords);
          })

          if (host) landAmount = landAmount + 1;
        
          hasBreak = true;

          return landAmount
        }
      }

      const newLands = lands.map(row => row.slice());
      newLands.map((l, row) => (
        l.map((_, column) => checkLands(newLands, [row, column], true))
      ));

      amount.value = landAmount;
    }

    handleLands(lands.value);

    const handleDimensionChange = (display) => {
     const arr = Array.from({ length: display.rows }).map((_, row) => {
       return Array.from({ length: display.columns }).map((_, col) => {
          
          const hasValue = lands.value[row]?.[col];

          return hasValue || 0;
        })
    })

      return arr
    }

    watch(display, () => {
      const arr = handleDimensionChange(display.value);
      lands.value = [...arr];
      handleLands(lands.value);
    }, { deep: true });

   
    const handleSelectLand = ([row, column]) => {
      const isSelected = lands.value[row][column];
      lands.value[row][column] = isSelected ? 0 : 1;
      handleLands(lands.value);
    }

    return {
      landAmount: amount,
      lands,
      display,
      handleSelectLand,
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  flex-direction: column;
}
.inputsContainer {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: auto;
}
.landsContainer {
  width: 100%;
  height: 100%;
  justify-content: center;
  display: grid;
  gap: 4px;
}
.land {
  width: 80px;
  height: 80px;
  border: 3px solid #D88627;
  border-radius: 8px;
  margin: 2px 0;
  cursor: pointer;
}
</style>
