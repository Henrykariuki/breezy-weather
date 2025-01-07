<script setup>
import { ref, onMounted } from 'vue';
import CityCard from '~/components/CityCard.vue'; // Adjust path if necessary

const APIKey = '687ef0bc53f11d315c123a4ce18647a4';
const SavedCities = ref([]);
const isLoading = ref(true);

const getCities = async () => {
    if (localStorage.getItem('savedCities')) {
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

// Fetch cities data after the component is mounted
onMounted(() => {
    getCities();
});
</script>

<template>
    <div>
        <!-- Show a loader while fetching data -->
        <div v-if="isLoading">Loading cities...</div>
        <div v-else>
            <div v-for="city in SavedCities" :key="city.id">
                <CityCard :city="city" />
            </div>
            <!-- Display a message if no valid cities are found -->
            <div v-if="!SavedCities.length">No valid cities found.</div>
        </div>
    </div>
</template>
