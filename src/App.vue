<script setup>
import { ref } from 'vue'
import Cell from './Cell.vue'
const size = 32
let gameCanPlayed = true
const isStart = ref(false)
let timer = null
function createGrid(size) {
  return Array.from({ length: size }, (_, row) =>
    Array.from({ length: size }, (_, col) => ({
      row,
      col,
      value: 0,
    })),
  )
}
const grid = ref(createGrid(size))

function howManyNeighbour(row, col, size, grid) {
  let neighbours = 0
  if (row - 1 >= 0) neighbours += grid[row - 1][col].value
  if (row - 1 >= 0 && col + 1 < size) neighbours += grid[row - 1][col + 1].value
  if (col + 1 < size) neighbours += grid[row][col + 1].value
  if (row + 1 < size && col + 1 < size) neighbours += grid[row + 1][col + 1].value
  if (row + 1 < size) neighbours += grid[row + 1][col].value
  if (row + 1 < size && col - 1 >= 0) neighbours += grid[row + 1][col - 1].value
  if (col - 1 >= 0) neighbours += grid[row][col - 1].value
  if (row - 1 >= 0 && col - 1 >= 0) neighbours += grid[row - 1][col - 1].value
  return neighbours
}

function handleNext() {
  if (gameCanPlayed) {
    const gridDup = grid.value.map((row) => row.map((cell) => ({ ...cell })))
    let totalNeighbours = 0
    // do operation in gridDup taking ref of actual grid
    for (let i = 0; i < size; i++) {
      for (let j = 0; j < size; j++) {
        const neighbours = howManyNeighbour(i, j, size, grid.value)
        totalNeighbours += neighbours
        if (grid.value[i][j].value === 1) {
          if (neighbours === 0 || neighbours === 1) gridDup[i][j].value = 0
          else if (neighbours >= 4) gridDup[i][j].value = 0
          else if (neighbours === 2 || neighbours === 3) gridDup[i][j].value = 1
        } else {
          if (neighbours === 3) gridDup[i][j].value = 1
        }
      }
    }
    if (totalNeighbours === 0) gameCanPlayed = false
    grid.value = gridDup
  } else {
    clearInterval(timer)
    timer = null
    isStart.value = false
  }
}

function clickCell(row, col) {
  grid.value[row][col].value = grid.value[row][col].value === 0 ? 1 : 0
}

function handleReset() {
  clearInterval(timer)
  isStart.value = false
  timer = null
  grid.value = createGrid(size)
}

function handleStart() {
  if (isStart.value) {
    clearInterval(timer)
    timer = null
    isStart.value = false
  } else {
    if (!timer) {
      timer = setInterval(() => {
        handleNext()
      }, 500)
    }
    isStart.value = true
  }
}
</script>

<template>
  <div class="flex justify-center items-center flex-col h-screen">
    <div class="p-1 bg-slate-50 drop-shadow-lg/30">
      <div v-for="row in grid" :key="row[0].id" class="flex flex-row justify-center">
        <Cell
          v-for="cell in row"
          :key="cell.id"
          :row="cell.row"
          :col="cell.col"
          :value="cell.value"
          :clickCell="clickCell"
        />
      </div>
    </div>
    <div class="flex gap-4">
      <p><button class="px-4 py-2 border-2 black" @click="handleReset">RESET</button></p>
      <p>
        <button class="px-4 py-2 border-2 black" @click="handleStart">
          {{ isStart ? 'STOP' : 'START' }}
        </button>
      </p>
      <p><button class="px-4 py-2 border-2 black" @click="handleNext">NEXT</button></p>
    </div>
  </div>
</template>
