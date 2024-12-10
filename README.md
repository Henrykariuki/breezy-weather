<script setup>
import { ref, computed, onMounted } from "vue";

const mapboxAPIKey = "pk.eyJ1Ijoid2FiaW8taGVuZHJpa2UiLCJhIjoiY20zeWJheDQ3MWZibDJycjMwYzR4MXB2ciJ9.qjd0VqaPNuBPlYvfXHM7YA";

// Input query
const searchQuery = ref("");
const results = ref([]);
const showNoResultsMessage = ref(false);
const latitude = ref(null); // Default latitude
const longitude = ref(null); // Default longitude

// Computed URL with the latest search query
const url = computed(() => {
    return `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place&proximity=${longitude.value},${latitude.value}`;
});

// Make API call
const { data, execute, status } = useLazyFetch(url, {
    immediate: false,
});

// Fetch and store search results
const getSearchResults = () => {
    if (searchQuery.value.trim() !== "") {
        execute().then(() => {
            // Access features array from the response data
            results.value = data.value?.features || [];

            // If no results, show the message and hide it after 2 seconds
            if (results.value.length === 0) {
                showNoResultsMessage.value = true;
                setTimeout(() => {
                    showNoResultsMessage.value = false;
                }, 2000);
            }
        });
        return;
    }
    results.value = null;
    showNoResultsMessage.value = false; // Hide message when input is cleared
};

// Get user's location dynamically
const getUserLocation = () => {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
            (position) => {
                latitude.value = position.coords.latitude;
                longitude.value = position.coords.longitude;
            },
            (error) => {
                console.error("Error getting user location:", error);
            }
        );
    } else {
        console.error("Geolocation is not supported by this browser.");
    }
};

// Fetch location on app load
onMounted(() => {
    getUserLocation();
});
</script>

<template>
    <div class="mt-5 mx-auto text-black">
        <div class="px-8 relative">
            <input v-model="searchQuery" @input="getSearchResults"
                class="py-2 px-2 w-full bg-transparent border-b focus:outline-none focus:border-gray-200 focus:shadow-[0px_1px_0_0_#adb5bd]"
                type="text" placeholder="Search" />
            <div v-if="status.pending">Loading...</div>
            <div class="py-2 text-white" v-if="status.error">
                Sorry, something went wrong, please try again
            </div>
            <div class="py-2 text-white" v-if="!status.pending && !status.error && showNoResultsMessage">
                No results match your query, try a different term
            </div>
            <template v-else>
                <NuxtLink :to="`/cityview/${place.place_name.replaceAll(' ', '')}`" v-for="place in results"
                    :key="place.id" class="bg-gray-100 p-2 cursor-pointer">
                    <p>{{ place.place_name }}</p>
                </NuxtLink>
            </template>
        </div>
    </div>
</template>

<style scoped></style>









<script setup>
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'

// Reactive variables
const route = useRoute()
const weatherData = ref(null) // Holds weather data
const loading = ref(true) // Tracks loading state
const error = ref(null) // Tracks errors

// API key (should ideally be in .env)
const API_KEY = '33fc5759adaa7d99fc938baa09fb423d'

// Fetch weather data
const fetchWeatherData = async () => {
    try {
        const { data } = await useFetch(
            `https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude=part&appid=${API_KEY}`
        )
        weatherData.value = data.value
    } catch (err) {
        error.value = 'Failed to fetch weather data. Please try again.'
        console.error(err)
    } finally {
        loading.value = false
    }
}

// Call the function on setup
await fetchWeatherData()

// Computed: Add calculated times to the weather data
const computedWeatherData = computed(() => {
    if (!weatherData.value) return null

    const localOffset = new Date().getTimezoneOffset() * 60000
    const timezoneOffset = weatherData.value.timezone_offset * 1000

    // Add current time calculation
    const currentTime =
        weatherData.value.current.dt * 1000 + localOffset + timezoneOffset

    // Add hourly weather time calculation
    const hourlyData = weatherData.value.hourly.map((hour) => {
        const hourTime = hour.dt * 1000 + localOffset + timezoneOffset
        return {
            ...hour,
            currentTime: hourTime,
        }
    })

    return {
        ...weatherData.value,
        currentTime,
        hourly: hourlyData,
    }
})
</script>

<template>
    <div>
        <p>City View</p>
        <!-- Show loading state -->
        <div v-if="loading">Loading weather data...</div>

        <!-- Show error if exists -->
        <div v-else-if="error">{{ error }}</div>

        <!-- Show weather data -->
        <div v-else-if="computedWeatherData">
            <p>Current Time: {{ new Date(computedWeatherData.currentTime).toLocaleString() }}</p>
            <h3>Hourly Forecast:</h3>
            <ul>
                <li v-for="hour in computedWeatherData.hourly" :key="hour.dt">
                    {{ new Date(hour.currentTime).toLocaleTimeString() }} - Temp: {{ hour.temp }}°C
                </li>
            </ul>
        </div>
    </div>
</template>

<style scoped>
/* Add custom styles if needed */
</style>
