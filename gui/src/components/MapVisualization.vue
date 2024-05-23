<template>
  <div class="map-visualization">
    <h1>Enter Location Coordinates</h1>
    <input v-model.number="latitude" placeholder="Latitude (e.g., 37.7749)" type="number">
    <input v-model.number="longitude" placeholder="Longitude (e.g., -122.4194)" type="number">
    <p v-if="remainingUsesMessage">{{ remainingUsesMessage }}</p>
    <button @click="displayMap">Show Map</button>
    <p v-if="errorMessage">{{ errorMessage }}</p>
    <div v-html="mapImage"></div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const latitude = ref('');
const longitude = ref('');
const mapImage = ref('');
const errorMessage = ref('');
const remainingUsesMessage = ref('');
const usageLimit = 1000; // Free usage limit

const getUsageCount = () => {
  const usage = localStorage.getItem('mapUsageCount');
  return usage ? parseInt(usage, 10) : 0;
};

const incrementUsageCount = () => {
  const currentUsage = getUsageCount();
  localStorage.setItem('mapUsageCount', currentUsage + 1);
};

const updateRemainingUsesMessage = () => {
  const remainingUses = usageLimit - getUsageCount();
  remainingUsesMessage.value = `Remaining uses this month: ${remainingUses}`;
};

const displayMap = () => {
  const currentUsage = getUsageCount();

  if (currentUsage >= usageLimit) {
    errorMessage.value = 'Usage limit exceeded. Please try again next month.';
    mapImage.value = '';
  } else if (latitude.value && longitude.value) {
    const mapUrl = `https://api.mapbox.com/styles/v1/mapbox/satellite-v9/static/${longitude.value},${latitude.value},15/600x300?access_token=ADD_Token_HERE`;
    mapImage.value = `<img src="${mapUrl}" alt="Satellite view of the coordinates" style="width:100%; height:auto;"/>`;
    incrementUsageCount();
    updateRemainingUsesMessage();
    errorMessage.value = '';
    console.log(`Map URL: ${mapUrl}`);
  } else {
    mapImage.value = '<p>Please enter both latitude and longitude.</p>';
    errorMessage.value = 'Latitude and longitude are required.';
  }
};

// Initialize remaining uses message on load
updateRemainingUsesMessage();
</script>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
  color: #ffffff;
}

.map-visualization input {
  display: block;
  margin: 10px 0;
  padding: 10px;
  font-size: 1rem;
}

.map-visualization button {
  padding: 10px 20px;
  font-size: 1rem;
  cursor: pointer;
}

.map-visualization div {
  margin-top: 20px;
}

p {
  color: #ffffff;
}

@media (min-width: 1024px) {
  h1 {
    text-align: left;
  }
}
</style>
