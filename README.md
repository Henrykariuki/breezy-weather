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
const computedWeatherData = computed(() => {
    if (!weatherData.value) return null;

    //Calculate time and date
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const currentUTC = weatherData.value.current.dt * 1000 + localOffset;
    const localTime = new Date(currentUTC + 1000 * weatherData.value.timezone_offset);

    const formattedDate = localTime.toLocaleDateString(undefined, {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric',
    });
    const formattedTime = localTime.toLocaleTimeString(undefined, {
        hour: '2-digit',
        minute: '2-digit',
        hour12: true,
    });

    // Map through hourly data and format it
    const hourlyData = weatherData.value.hourly.map((hour) => {
        const utcTime = hour.dt * 1000 + localOffset;
        const localTime = new Date(utcTime + 1000 * weatherData.value.timezone_offset);

        return {
            time: localTime.toLocaleTimeString("en-us", { hour: '2-digit', hour12: true }),
            temperature: (hour.temp - 273.15),
            icon: `https://openweathermap.org/img/wn/${hour.weather[0].icon}@2x.png`,
            description: hour.weather[0].description,
        };
    });


    //Map through daily array and format it
    const dailyData = weatherData.value.daily.map((day) => {
        const utcTime = day.dt * 1000 + localOffset
        const localTime = new Date(utcTime + 1000 * weatherData.value.timezone_offset)

        return {
            time: localTime.toLocaleDateString("en-us", { 
                weekday: "long",
                month: "long",
                day: "numeric"
            }),
            maxTemp: day.temp.max - 273.15, // conver Kelvin into celcius
            minTemp: day.temp.min - 273.15, // conver Kelvin into celcius
            icon: `https://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`,
            description: day.weather[0].description
        }
    })


    const icon = weatherData.value.current.weather[0].icon;
    const iconUrl = `https://openweathermap.org/img/wn/${icon}@2x.png`;

    const temperatureInCelcius = weatherData.value.current.temp - 273.15;
    const feelsLikeInCelcius = weatherData.value.current.feels_like - 273.15;

    return {
        temperature: temperatureInCelcius,
        weatherDescription: weatherData.value.current.weather[0].description,
        iconUrl,
        feelsLike: feelsLikeInCelcius,
        windSpeed: weatherData.value.current.wind_speed,
        humidity: weatherData.value.current.humidity,
        formattedDate,
        formattedTime,
        hourly: hourlyData,
        daily: dailyData
    };
});


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
};

console.log(weatherData)
</script>

<template>
    <div class="pt-8 bg-[url('/images/pexels01.jpg')] bg-cover bg-center">
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
            <p class="text-xl font-semibold">{{ route.params.id }}</p>
            <!--Time and date-->
            <p class="text-sm">{{ computedWeatherData.formattedDate }}, {{ computedWeatherData.formattedTime }}</p>
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
        <div class="border border-black">
            <h2 class="text-lg font-bold mb-4">Hourly Forecast</h2>
            <div v-for="hour in computedWeatherData.hourly" :key="hour.time">
                <p class="text-sm">{{ hour.time }}</p>
                <img class="w-[50px]" :src="hour.icon" :alt="hour.description">
                <p class="text-sm font-semibold">{{ Math.round(hour.temperature) }}°C</p>
                <p class="text-xs">{{ hour.description }}</p>
            </div>
        </div>
        <div class="border border-black">
            <h2>Daily Forcast</h2>
            <div v-for="day in computedWeatherData.daily" :key="data.time">
                <p class="text-sm">{{ day.time }}</p>
                <p class="text-sm font-semibold">{{ Math.round(day.maxTemp) }}°C</p>
                <p class="text-sm font-semibold">{{ Math.round(day.minTemp) }}°C</p>
                <img class="w-[50px]" :src="day.icon" :alt="day.description">
                <p class="text-sm">{{ day.description }}</p>
            </div>
        </div>
    </div>
</template>