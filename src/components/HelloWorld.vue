<script setup>
import PriceChart from './PriceChart.vue'

import { ref, onMounted } from 'vue'

const data = ref(null)
const grouped = ref({})
const loading = ref(true)

function groupByTicker(array) {
  return array.reduce((acc, item) => {
    if (!acc[item.ticker]) acc[item.ticker] = []
    acc[item.ticker].push(item)
    return acc
  }, {})
}

onMounted(async () => {
  try {
    const response = await fetch(`${import.meta.env.BASE_URL}json/export.json`)
    const result = await response.json()
    data.value = result
    grouped.value = groupByTicker(result)
    loading.value = false
  } catch (error) {
    console.error('Error fetching data:', error)
    loading.value = false
  }
})
</script>
<template>
  <div v-if="!loading">
    <div v-for="(rows, ticker) in grouped" :key="ticker" style="margin-bottom: 2em;">
      <h2>{{ ticker }}</h2>
      <PriceChart :rows="rows" />
      <!-- You can keep the table if you want -->
      
    </div>
  </div>
  <div v-else>Loading...</div>
</template>


