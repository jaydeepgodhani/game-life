<script setup>
import { defineProps } from 'vue'
const props = defineProps({
  grid: {
    type: Object, // The type will be an object containing the ref's value
    default: null,
  },
})
</script>

<template>
  <div></div>
</template>
