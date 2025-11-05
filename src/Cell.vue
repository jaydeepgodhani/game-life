<script setup>
import { defineProps } from 'vue'
const props = defineProps({
  row: {
    type: Number,
    required: true,
  },
  col: {
    type: Number,
    required: true,
  },
  value: {
    type: Number,
    required: true,
  },
  clickCell: {
    type: Function,
    required: true,
  },
})

function handleClick(row, col) {
  props.clickCell(row, col)
}
</script>

<template>
  <span
    class="h-[1rem] w-[1rem] outline-1 flex items-center justify-center"
    :style="{ backgroundColor: props.value === 0 ? 'white' : 'orange' }"
    @click="handleClick(props.row, props.col)"
  >
  </span>
</template>
