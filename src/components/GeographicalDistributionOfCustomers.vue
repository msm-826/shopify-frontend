<template>
  <div class="map-container">
    <div id="map" style="height: 500px;"></div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import axios from 'axios';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';

const customerData = ref([]);

const fetchCustomerData = async () => {
  try {
    const response = await axios.get('https://shopifybackend-jv99.onrender.com/api/geographical-distribution');
    customerData.value = response.data;
  } catch (error) {
    console.error('Error fetching customer data:', error);
  }
};

const initMap = () => {
  const map = L.map('map').setView([39.8283, -98.5795], 4); // Center on US

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors'
  }).addTo(map);

  customerData.value.forEach(location => {
    getCoordinates(location.city, location.province).then(coords => {
      if (coords) {
        L.marker(coords)
          .addTo(map)
          .bindPopup(`<b>${location.city}, ${location.province}</b><br>Customers: ${location.customer_count}`);
      }
    });
  });
};

const getCoordinates = async (city, province) => {
  try {
    const response = await axios.get(`https://nominatim.openstreetmap.org/search?format=json&q=${city},${province},USA`);
    if (response.data && response.data.length > 0) {
      return [parseFloat(response.data[0].lat), parseFloat(response.data[0].lon)];
    }
  } catch (error) {
    console.error('Error getting coordinates:', error);
  }
  return null;
};

onMounted(async () => {
  await fetchCustomerData();
  initMap();
});
</script>

<style scoped>
@import 'leaflet/dist/leaflet.css';

.map-container {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
}
</style>