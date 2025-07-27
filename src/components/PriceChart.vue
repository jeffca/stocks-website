<script setup>
import { Line } from 'vue-chartjs'
import { Chart, Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale } from 'chart.js'
import { computed } from 'vue'

Chart.register(Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale)

const props = defineProps({
  rows: Array,
  ticker: String
})
const chartData = computed(() => {
  const sorted = [...props.rows].sort((a, b) => {
    return new Date(`${a.date} ${a.time}`) - new Date(`${b.date} ${b.time}`)
  })

  const prices = sorted.map(row => row.price)
  const firstPrice = prices[0]
  const lastPrice = prices[prices.length - 1]

  // Set color: red if last < first, green otherwise
  const color = lastPrice < firstPrice ? '#d32f2f' : '#023d10'

  return {
    labels: sorted.map(row => `${row.date} ${row.time}`),
    datasets: [
      {
        label: 'Price',
        data: prices,
        borderColor: color,
        backgroundColor: color + '33', // semi-transparent fill
        tension: 0.2
      }
    ]
  }
})


const chartOptions = {
  responsive: true,
  plugins: {
    legend: {
      display: false
    }
  },
  scales: {
    x: {
      ticks: {
        display: false // This hides the x-axis (row) labels
      }
    }
  }
}
</script>

<template> 
  <h2>{{ ticker }}</h2>
  <Line :data="chartData" :options="chartOptions" />
</template>
