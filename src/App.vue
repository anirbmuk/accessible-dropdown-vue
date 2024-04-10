<template>
  <header>Accessible Dropdown Demo</header>
  <main>
    <div class="content">
      <DropDown :options="options" v-model="selected" />
    </div>
    <div>Selected: {{ selected || 'none' }}</div>
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import DropDown from './components/DropDown.vue'

const options = [
  'Jan',
  'Feb',
  'Mar',
  'Apr',
  'May',
  'Jun',
  'Jul',
  'Aug',
  'Sep',
  'Oct',
  'Nov',
  'Dec'
] satisfies string[]
const selected = ref<string>('')
</script>

<style scoped>
.content {
  width: 9rem;
  margin: auto;
}
</style>
