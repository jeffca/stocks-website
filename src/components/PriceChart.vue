<script setup>
import { Line } from 'vue-chartjs'
import { Chart, Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale } from 'chart.js'
import { computed } from 'vue'

Chart.register(Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale)

const props = defineProps({
  rows: Array
})

// Prepare data for the chart
const chartData = computed(() => {
  // Sort by date and time for correct order
  const sorted = [...props.rows].sort((a, b) => {
    return new Date(`${a.date} ${a.time}`) - new Date(`${b.date} ${b.time}`)
  })

  return {
    labels: sorted.map(row => `${row.date} ${row.time}`),
    datasets: [
      {
        label: 'Price',
        data: sorted.map(row => row.price),
        borderColor: 'rgba(75, 192, 192, 1)',
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        tension: 0.2
      }
    ]
  }
})

const chartOptions = {
  responsive: true,
  plugins: {
    legend: {
      display: true
    }
  }
}
</script>

<template>
  <Line :data="chartData" :options="chartOptions" />
</template>
