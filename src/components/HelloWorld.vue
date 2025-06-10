<script setup>
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
      <table>
        <thead>
          <tr>
            <th>Date</th>
            <th>Time</th>
            <th>Price</th>
            <th>Type of Day</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in rows" :key="row.timestamp">
            <td>{{ row.date }}</td>
            <td>{{ row.time }}</td>
            <td>{{ row.price }}</td>
            <td>{{ row.type_of_day }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
  <div v-else>Loading...</div>
</template>
