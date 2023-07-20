<script setup lang="ts">
import { useMagicKeys, useMouse, useMousePressed } from '@vueuse/core'
import { ref, watch } from 'vue'

interface Point {
  x: number
  y: number
}

const div = ref(null)
const { x, y } = useMouse()
const { pressed } = useMousePressed({ target: div })

const keys = useMagicKeys()
const ctrlZ = keys['Ctrl+z']
const ctrlY = keys['Ctrl+y']

const points = ref<Point[]>([])
const history = ref<Point[]>([])

watch(pressed, () => {
  if (pressed.value) {
    points.value.push({ x: x.value, y: y.value })
  }
})
watch(ctrlZ, (e) => {
  if (e) {
    history.value.push(points.value[points.value.length - 1])
    points.value.pop()
  }
})
watch(ctrlY, (e) => {
  if (e) {
    if (history.value.length > 0) {
      points.value.push(history.value[history.value.length - 1])
      history.value.pop()
    }
  }
})
</script>

<template>
  <main ref="div">
    <div class="point" v-for="point in points" :style="{ top: point.y + 'px', left: point.x + 'px' }"></div>
  </main>
</template>

<style scoped>
main {
  width: 100vw;
  height: 100vh;

  position: relative;
}

.point {
  position: absolute;

  width: 5px;
  height: 5px;
  border-radius: 5px;

  background-color: white;
}
</style>
