<script setup>
import { data } from 'autoprefixer'
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()

const API_KEY = '687ef0bc53f11d315c123a4ce18647a4'
const { data: weatherData, status, error } = await useFetch(

    `https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&appid=${API_KEY}`
)

const ShowSkeleton = ref(true)

//Set a delay to display skeleton for a minum time
setTimeout(() => {
    ShowSkeleton.value = false
}, 1000)

const isWeatherDataPopulated = computed(() => weatherData.value)

console.log('Is weather data populated:', isWeatherDataPopulated.value)


// Computed: Add calculated times to the weather data
/*const computedWeatherData = computed(() => {
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

    // convert temperatures from kelvin to celcius 
    const temperatureInCelcius = weatherData.value.main.temp - 273.15
    const feelsLikeInCelcius = weatherData.value.main.feels_like - 273.15

    return {
        cityName: weatherData.value.name,
        temperature: temperatureInCelcius,
        weatherDescription: weatherData.value.weather[0].description,
        iconUrl,
        feelsLike: feelsLikeInCelcius,
        windSpeed: weatherData.value.wind.speed,
        humidity: weatherData.value.main.humidity,
        localTime,
    };
})

const router = useRouter()

const removeCity = () => {
    const savedCities = JSON.parse(localStorage.getItem('savedCities'));

    // Log the current state of saved cities
    console.log('Saved Cities Before Removal:', savedCities);

    // Retrieve the city ID from the query parameter
    const cityIdToRemove = route.query.id;

    // Log the city ID to remove
    console.log('City ID to Remove:', cityIdToRemove);

    // Validate the city ID
    if (!cityIdToRemove) {
        console.error('Invalid city ID:', cityIdToRemove);
        return; // Stop execution if the city ID is invalid
    }

    // Filter out the city with the matching ID (use string comparison)
    const updatedCities = savedCities.filter((city) => city.id !== cityIdToRemove);

    // Log the updated list of cities
    console.log('Updated Cities After Removal:', updatedCities);

    // Update localStorage with the new list
    localStorage.setItem('savedCities', JSON.stringify(updatedCities));

    // Log the updated state of localStorage
    console.log('Saved Cities in Local Storage:', JSON.parse(localStorage.getItem('savedCities')));

    // Redirect the user to the home page
    router.push('/');
};*/

console.log(weatherData)
</script>

<template>
    <div class="pt-8 h-screen">
        <div class="w-full text-center py-4 mt-6 bg-gray-100 text-black" v-if="route.query.preview">
            <p>You are currently viewing this city. Click the "+" icon to start tracking it.</p>
        </div>
        <!-- Show loading state -->
        <div v-if="ShowSkeleton">
            <CityViewSkeleton />
        </div>

        <!-- Show error if exists -->
        <div v-if="error">{{ error }}</div>

        <!-- Show weather data -->
        <div class="border-b border-slate-200  flex items-center flex-col gap-4"
         v-else-if="computedWeatherData && !ShowSkeleton">
            <p class="text-xl font-semibold">{{ computedWeatherData.cityName }}</p>
            <p class="text-sm">{{ computedWeatherData.localTime.toLocaleString() }}</p>
            <p class="text-7xl font-semibold">{{ Math.round(computedWeatherData.temperature) }}°</p>
            <div class="text-sm flex flex-col items-center gap-4">
                <div class="flex flex-col items-center">
                    <p>Feels like {{ Math.round(computedWeatherData.feelsLike) }}°</p>
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
            <div class="hover:text-red-500 mb-4 text-lg duration-150" @click="removeCity">Remove city</div>
        </div>
    </div>
</template>