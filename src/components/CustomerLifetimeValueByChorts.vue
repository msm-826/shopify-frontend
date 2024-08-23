<script setup>
import { ref, onMounted, computed } from 'vue'
import axios from 'axios'
import { Line, Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  BarElement,
  Title,
  Tooltip,
  Legend,
} from 'chart.js'

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  BarElement,
  Title,
  Tooltip,
  Legend
)

let rawData = ref(null)

const fetchData = async () => {
  try {
    const response = await axios.get('https://shopifybackend-jv99.onrender.com/api/customer-lifetime-value')
    rawData.value = response.data
  } catch (error) {
    console.error('Error fetching data:', error)
    rawData.value = []
  }
}

const chartData = computed(() => ({
  labels: rawData.value?.map(item => item.cohort) || [],
  datasets: [{
    label: "Customer Lifetime Value",
    data: rawData.value?.map(item => item.clv) || [],
    backgroundColor: 'rgba(75, 192, 192, 0.6)',
    borderColor: 'rgba(75, 192, 192, 1)',
    borderWidth: 1
  }],
}))

const chartOptions = {
  responsive: true,
  maintainAspectRatio: true
}

const chartTypes = [
  { label: 'Line Chart', value: Line },
  { label: 'Bar Chart', value: Bar },
]

let activeChart = ref(chartTypes[0]);

const setChartType = (chartType) => {
  activeChart.value = chartTypes[chartType];
}

onMounted(() => {
  fetchData()
})
</script>

<template>
  <div class="chart-type-buttons">
    <button v-for="(chartType, index) in chartTypes" :key="index" @click="setChartType(index)"
      :class="{ active: activeChart.label === chartType.label }">
      {{ chartType.label }}
    </button>
  </div>
  <component v-if="chartData.datasets[0].data.length > 0" :is="activeChart.value" :data="chartData"
    :options="chartOptions" />
  <p v-else>Loading data...</p>
</template>

<style>
.chart-type-buttons {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.chart-type-buttons button {
  flex-grow: 1;
  background-color: #f0f0f0;
  color: #333;
}

.chart-type-buttons button.active {
  background-color: #4CAF50;
  color: white;
}
</style>