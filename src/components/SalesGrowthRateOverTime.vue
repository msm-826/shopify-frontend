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

const fetchData = async (interval) => {
  try {
    const response = await axios.get(`https://shopifybackend-jv99.onrender.com/api/total-sales/?interval=${interval.value}`)
    calculateSalesGrowthRate(response.data)
  } catch (error) {
    console.error('Error fetching data:', error)
  }
}

let salesGrowthData = ref([])

const calculateSalesGrowthRate = (data) => {
  salesGrowthData.value = data.map((item, index) => {
    if (index === 0) {
      return { date: item._id, salesGrowthRate: 0 }
    }
    const previousSales = data[index - 1].total_sales
    const currentSales = item.total_sales;
    const salesGrowthRate = (currentSales - previousSales) / previousSales;
    return { date: item._id, salesGrowthRate }
  })
}

const chartData = computed(() => ({
  labels: salesGrowthData.value.map((item) => item.date),
  datasets: [{
    label: "Sales Growth Rate Over Time",
    data: salesGrowthData.value.map(item => item.salesGrowthRate),
    backgroundColor: 'rgba(75, 192, 192, 0.6)',
    borderColor: 'rgba(75, 192, 192, 1)',
    borderWidth: 1
  }],
}))

const chartOptions = {
  responsive: true,
  maintainAspectRatio: true
}

const intervals = [
  { label: 'Daily', value: 'daily' },
  { label: 'Monthly', value: 'monthly' },
  { label: 'Quarterly', value: 'quarterly' },
  { label: 'Yearly', value: 'yearly' },
];

let activeInterval = ref('daily');

const setInterval = (interval) => {
  activeInterval.value = interval;
  fetchData(activeInterval);
}

const chartTypes = [
  { label: 'Line Chart', value: Line },
  { label: 'Bar Chart', value: Bar },
]

let activeChart = ref(chartTypes[0]);

const setChartType = (chartType) => {
  activeChart.value = chartTypes[chartType];
  console.log(`Active Interval (setChartType): ${activeInterval.value}`)
  console.log(`Active Chart (setChartType): ${activeChart.value}`)
}

onMounted(() => {
  fetchData(activeInterval)
})
</script>

<template>
  <div class="interval-buttons">
    <button v-for="(interval, index) in intervals" :key="index" @click="setInterval(interval.value)"
      :class="{ active: activeInterval === interval.value }">
      {{ interval.label }}
    </button>
  </div>
  <div class="chart-type-buttons">
    <button v-for="(chartType, index) in chartTypes" :key="index" @click="setChartType(index)"
      :class="{ active: activeChart.label === chartType.label }">
      {{ chartType.label }}
    </button>
  </div>
  <component v-if="chartData.datasets[0].data.length > 0" :is="activeChart.value" :data="chartData" :options="chartOptions" />
  <p v-else>Loading data...</p>
</template>

<style>
.interval-buttons, .chart-type-buttons {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.interval-buttons button, .chart-type-buttons button {
  flex-grow: 1;
  background-color: #f0f0f0;
  color: #333;
}

.interval-buttons button.active, .chart-type-buttons button.active {
  background-color: #4CAF50;
  color: white;
}
</style>