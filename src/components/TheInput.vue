<script setup lang="ts">
import { ref } from 'vue'

const { modelValue } = defineModels<{
  modelValue: number
}>()

const MAX_VALUE = 1000000000000000000
const showMaxWarning = ref(false)

function handleInput(e: Event) {
  const input = e.target as HTMLInputElement
  const value = Number(input.value)
  if (value > MAX_VALUE) {
    input.value = MAX_VALUE.toString()
    modelValue.value = MAX_VALUE
    showMaxWarning.value = true
    setTimeout(() => {
      showMaxWarning.value = false
    }, 3000)
  }
  else {
    showMaxWarning.value = false
  }
}
</script>

<template>
  <div class="space-y-2">
    <input
      id="input"
      v-model="modelValue"
      v-bind="$attrs"
      type="number"
      :max="MAX_VALUE"
      p="x-4 y-2"
      w="250px"
      text="center"
      bg="transparent"
      border="~ rounded white dark:white"
      outline="none active:none"
      @input="handleInput"
    >

    <transition name="fade">
      <div
        v-if="showMaxWarning"
        class="rounded-md bg-red-50 px-4 py-2 text-sm text-red-600"
      >
        Maximum value reached (1 quintillion)
      </div>
    </transition>
  </div>
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
