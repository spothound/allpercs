<script setup lang="ts">
import { max } from 'node_modules/cypress/types/lodash'
import { ref } from 'vue'

defineOptions({
  name: 'IndexPage',
})

function formatNumber(value: number) {
  // Ensure the value is a number
  value = Number(value)
  if (Number.isNaN(value))
    return 'N/A'

  const formats = [
    { threshold: 1e9, suffix: 'B', divisor: 1e9 },
    { threshold: 1e6, suffix: 'M', divisor: 1e6 },
    { threshold: 1e3, suffix: 'K', divisor: 1e3 },
  ]

  for (const { threshold, suffix, divisor } of formats) {
    if (value >= threshold) {
      const formatted = (value / divisor).toFixed(2).replace(/\.00$/, '')
      return `${formatted}${suffix}`
    }
  }

  return value.toFixed(2).replace(/\.00$/, '')
}

const user = useUserStore()
const name = ref(user.savedName)

const router = useRouter()
function go() {
  if (name.value)
    router.push(`/hi/${encodeURIComponent(name.value)}`)
}

const { t } = useI18n()

const MAX_VALUE = 1000000000000000000 // 1 quintillion
const inputValue = ref('')
const percentages = ref([])

function validateInput() {
  const value = Number(inputValue.value)
  if (value > MAX_VALUE) {
    inputValue.value = MAX_VALUE.toString()
  }
}

function calculatePercentages() {
  const value = Number(inputValue.value)
  const percents = [...Array.from({ length: 100 })].map((_, i) =>
    ((value * (100 - i)) / 100),
  )
  percentages.value = percents
}

watch(inputValue, () => {
  validateInput()
  calculatePercentages()
})

const isMultiple = number => number % 5 === 0 || number % 10 === 0
const isBigInput = computed(() => inputValue.value > 9999999999)
</script>

<template>
  <div>
    <div text-4xl>
      <div i-carbon-campsite inline-block />
    </div>
    <p>
      <a rel="noreferrer" href="https://github.com/antfu/vitesse" target="_blank">
        Allpercs
      </a>
    </p>
    <p>
      <em text-sm opacity-75>Visualize all percentages easily</em>
    </p>
    <TheInput
      v-model="inputValue"
      placeholder="Enter a number"
      autocomplete="false"
      @keydown.enter="go"
    />
    <p v-if="isBigInput" class="mt-2 text-lg text-white">
      Big nummber, formated input: <br> {{ formatNumber(inputValue) }}
    </p>
    <div class="grid grid-cols-10 mt-8 gap-4">
      <div
        v-for="(percentage, index) in percentages"
        :key="index"
      >
        {{ 100 - index }}%:
        <div
          class="rounded p-2 text-center"
          :class="[
            isMultiple(100 - index) ? 'bg-white text-blue-600 font-bold' : 'bg-white text-blue-600 font-bold',
          ]"
        >
          {{ isBigInput ? formatNumber(percentage) : percentage }}
        </div>
      </div>
    </div>

    <div py-4 />
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>
