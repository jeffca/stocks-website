<script setup>
import PriceChart from './PriceChart.vue'

import { ref, onMounted } from 'vue'

const data = ref(null)
const grouped = ref({})
const loading = ref(true)
const tickers = ref([]);
const selectedTicker = ref(null)

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
    tickers.value = Object.keys(grouped.value).sort(); 
    loading.value = false
  } catch (error) {
    console.error('Error fetching data:', error)
    loading.value = false
  }
})
</script>
<template>
  <div class="container">
    <div>
      <span 
        v-for="ticker in tickers" 
        :key="ticker"
        @click="selectedTicker = ticker"
        :class="{ selected: ticker === selectedTicker }"

        style="cursor:pointer;"
      >
        {{ ticker }}<br />
      </span>

    </div>
    <div v-if="!loading" class="charts">
      <div v-if="selectedTicker && !loading" class="charts">
        <PriceChart :rows="grouped[selectedTicker]" :ticker="selectedTicker" />
      </div>
    </div>
    <div v-else>Loading...</div>
  </div>

</template>

<style scoped>
.container {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
  gap: 16px;
  padding: 16px;  
  width: 100%;
  max-width: 1200px; /* or whatever fits your layout */
}

.charts {
  width: 550px;
}

  .charts canvas {
  width: 100% !important;
  min-height: 400px !important;
}

.selected {
  font-weight: bold;
  font-size: 1.2em;
}
</style>
