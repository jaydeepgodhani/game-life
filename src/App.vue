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
    grid.value = createGrid(size)
  }
}

function clickCell(row, col) {
  gameCanPlayed = true
  grid.value[row][col].value = grid.value[row][col].value === 0 ? 1 : 0
}

function handleReset() {
  clearInterval(timer)
  timer = null
  isStart.value = false
  grid.value = createGrid(size)
}

function handleStart() {
  if (isStart.value) {
    clearInterval(timer)
    timer = null
    isStart.value = false
    grid.value = createGrid(size)
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
  <div class="flex items-center flex-col h-screen">
    <h1 class="text-3xl mt-4">GAME OF LIFE</h1>
    <div class="p-2 bg-slate-50 drop-shadow-xl mt-4">
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
    <div class="flex gap-4 mt-4">
      <p>
        <button
          class="py-2 px-8 m-4 border-2 rounded-full text-xl flex items-center bg-white cursor-pointer transition duration-400 hover:drop-shadow-lg/30 hover:bg-red-200"
          @click="handleReset"
        >
          RESET
        </button>
      </p>
      <p>
        <button
          class="py-2 px-8 m-4 border-2 rounded-full text-xl flex items-center bg-white cursor-pointer transition duration-400 hover:drop-shadow-lg/30 hover:bg-blue-200"
          @click="handleStart"
        >
          {{ isStart ? 'STOP' : 'START' }}
        </button>
      </p>
      <p>
        <button
          class="py-2 px-8 m-4 border-2 rounded-full text-xl flex items-center bg-white cursor-pointer transition duration-400 hover:drop-shadow-lg/30 hover:bg-green-200"
          @click="handleNext"
        >
          NEXT
        </button>
      </p>
    </div>
    <div class="mt-4">
      <p>
        <b class="text-xl">For a space that is populated :</b><br /><br />
        Each cell with one or no neighbors dies, as if by solitude.<br />
        Each cell with four or more neighbors dies, as if by overpopulation.<br />
        Each cell with two or three neighbors survives.
      </p>
      <br />
      <p>
        <b class="text-xl">For a space that is empty or unpopulated:</b><br /><br />
        Each cell with three neighbors becomes populated.
      </p>
    </div>
  </div>
</template>
