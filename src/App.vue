<script setup>
import { ref } from 'vue';
import TotalSalesOverTime from './components/TotalSalesOverTime.vue';
import SalesGrowthRateOverTime from './components/SalesGrowthRateOverTime.vue';
import NumberOfRepeatCustomers from './components/NumberOfRepeatCustomers.vue';
import NewCustomersAddedOverTime from './components/NewCustomersAddedOverTime.vue';
import GeographicalDistributionOfCustomers from './components/GeographicalDistributionOfCustomers.vue';
import CustomerLifetimeValueByChorts from './components/CustomerLifetimeValueByChorts.vue';

const charts = [
  { name: 'Total sales over time', component: TotalSalesOverTime },
  { name: 'Sales growth rate over time', component: SalesGrowthRateOverTime },
  { name: 'New customers added over time', component: NewCustomersAddedOverTime },
  { name: 'Number of repeat customers', component: NumberOfRepeatCustomers },
  { name: 'Geographical distribution of customers', component: GeographicalDistributionOfCustomers },
  { name: 'Customer lifetime value by cohorts', component: CustomerLifetimeValueByChorts },
];

let activeChartComponent = ref(charts[0].component);
let activeChartName = ref(charts[0].name);
let activeChartIndex = ref(0);

const setActiveChart = (chart, index) => {
  activeChartComponent.value = chart.component;
  activeChartName.value = chart.name;
  activeChartIndex.value = index;
};
</script>

<template>
  <div class="chart-container">
    <div class="sidebar">
      <button 
        v-for="(chart, index) in charts" 
        :key="index"
        @click="setActiveChart(chart, index)"
        :class="{ active: activeChartIndex === index }"
        :title="chart.name"
      >
        {{ chart.name }}
      </button>
    </div>
    <div class="main-content">
      <component :is="activeChartComponent" />
    </div>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0;
  padding: 0;
  height: 100vh;
}

body {
  margin: 0;
  padding: 0;
}

.chart-container {
  display: flex;
  width: 100%;
  height: 100%;
}

.sidebar {
  width: 30%;
  padding: 20px;
  background-color: #f0f0f0;
  display: flex;
  flex-direction: column;
  gap: 10px;
  overflow-y: auto;
}

.main-content {
  width: 70%;
  padding: 20px;
  display: flex;
  flex-direction: column;
  flex-grow: 1;
}

button {
  padding: 10px 15px;
  font-size: 14px;
  cursor: pointer;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  transition: background-color 0.3s;
  text-align: left;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

button:hover, button.active {
  background-color: #0d7a12;
}

h2 {
  margin-bottom: 20px;
}

.main-content {
  height: 100%;
}
</style>