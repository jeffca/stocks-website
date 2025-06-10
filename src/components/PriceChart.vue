<script setup>
import { Line } from 'vue-chartjs'
import { Chart, Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale } from 'chart.js'
import { computed } from 'vue'

Chart.register(Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale)

const props = defineProps({
  rows: Array
})

const chartData = computed(() => {
  const sorted = [...props.rows].sort((a, b) => {
    return new Date(`${a.date} ${a.time}`) - new Date(`${b.date} ${b.time}`)
  })

  return {
    labels: sorted.map(row => `${row.date} ${row.time}`),
    datasets: [
      {
        label: 'Price',
        data: sorted.map(row => row.price),
        borderColor: '#a7b8c7',
        backgroundColor: '#a7b8c7',
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
  <Line :data="chartData" :options="chartOptions" />
</template>
