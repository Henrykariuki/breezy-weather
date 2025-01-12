<script setup>

import { ref, onMounted } from 'vue';
import CityCard from '~/components/CityCard.vue'; // Adjust path if necessary

const route = useRoute()



const APIKey = '687ef0bc53f11d315c123a4ce18647a4';
const SavedCities = ref([]);
const isLoading = ref(true);

const getCities = async () => {
  if (import.meta.client && localStorage.getItem('savedCities')) {
    SavedCities.value = JSON.parse(localStorage.getItem('savedCities'));
  }

  // Filter out cities with missing or invalid coordinates
  const validCities = SavedCities.value.filter(
    (city) => city.coords && city.coords.lat && city.coords.lng
  );

  if (!validCities.length) {
    isLoading.value = false; // Stop loading if no valid cities
    return;
  }

  const requests = validCities.map((city) =>
    $fetch(
      `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${APIKey}`
    )
  );

  try {
    const weatherData = await Promise.all(requests);

    weatherData.forEach((value, index) => {
      validCities[index].weather = value; // Assign weather data
    });

    SavedCities.value = validCities; // Update SavedCities with valid data only
  } catch (error) {
    console.error('Error fetching weather data:', error);
  }

  isLoading.value = false;
};


getCities();


</script>

<template>
  <div class="mx-6 h-screen">
    <!-- Show a loader while fetching data -->
    <div v-if="isLoading">
      <CitycardSkeleton/>
    </div>
    <div v-else>
      <NuxtLink :to="{ path: `/cityview/${city.weather.name}`,
         query: { 
          id: city.id,
          lat: city.coords.lat, 
          lng: city.coords.lng 
          } }" v-for="city in SavedCities" :key="city.id">
        <CityCard :city="city" />
      </NuxtLink>
      <!-- Display a message if no valid cities are found -->
      <div class="text-white" v-if="!SavedCities.length">No valid cities found.</div>
    </div>
  </div>
</template>
