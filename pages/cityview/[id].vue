<script setup>
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'

// Reactive variables
const route = useRoute()
const weatherData = ref(null)
const loading = ref(true)
const error = ref(null) 


const API_KEY = '687ef0bc53f11d315c123a4ce18647a4'


// Fetch weather data
const fetchWeatherData = async () => {
    try {
        const { data } = await useFetch(
            `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=${API_KEY}`
        )
        weatherData.value = data.value
        console.log(weatherData)
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

    // The `dt` value is in UTC seconds; convert to milliseconds for JavaScript
    const utcTime = weatherData.value.dt * 1000

    //icon url
    const icon = weatherData.value.weather[0].icon
    const iconUrl = `https://openweathermap.org/img/wn/${icon}@2x.png`

    // The `timezone` value is in seconds; convert to milliseconds
    const timezoneOffset = weatherData.value.timezone * 1000

    // Calculate the local time by adding the UTC time and the timezone offset
    const localTime = new Date(utcTime + timezoneOffset)
        

    return {
        cityName: weatherData.value.name,
        temperature: weatherData.value.main.temp,
        weatherDescription: weatherData.value.weather[0].description,
        iconUrl,
        feelsLike: weatherData.value.main.feels_like,
        windSpeed: weatherData.value.wind.speed,
        humidity: weatherData.value.main.humidity,
        localTime,
    };
})


</script>

<template>
    <div>
        <div class="w-full text-center py-4 mt-6 bg-gray-100 text-black"
         v-if="route.query.preview">
            <p>You are currently viewing this city. Click the "+" icon to start tracking it.</p>
        </div>
        <!-- Show loading state -->
        <div v-if="loading">Loading weather data...</div>

        <!-- Show error if exists -->
        <div v-else-if="error">{{ error }}</div>

        <!-- Show weather data -->
        <div class="border-b border-slate-200 flex items-center flex-col gap-4" v-else-if="computedWeatherData">
            <p class="text-xl font-semibold">{{ computedWeatherData.cityName }}</p>
            <p class="text-sm">{{ computedWeatherData.localTime.toLocaleString() }}</p>
            <p class="text-7xl font-semibold">{{ Math.round(computedWeatherData.temperature) }}°</p>
            <div class="text-sm flex flex-col items-center gap-4">
                <div class="flex flex-col items-center">
                    <p>Feels like {{ Math.round(computedWeatherData.feelsLike) }}</p>
                    <p>{{ computedWeatherData.weatherDescription }}</p>
                </div>
                <div class="flex flex-col items-center">
                    <p>Wind speed: {{ computedWeatherData.windSpeed }} m/s</p>
                    <p>Humidity: {{ computedWeatherData.humidity }}%</p>
                </div>
            </div>
            <div class="object-cover">
                <img class="w-[150px]" :src="computedWeatherData.iconUrl" :alt="computedWeatherData.weatherDescription">
            </div>
        </div>
    </div>
</template>