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
            time: localTime.toLocaleTimeString("en-us", { hour: '2-digit', hour12: false }),
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
    <div class=" grid grid-cols-4 bg-[url('/images/pexels02.jpg')] bg-cover bg-center">
        <div class="col-span-3 p-5 flex justify-center items-end ">
            <div class=" py-5 border border-black flex flex-row gap-4 overflow-x-auto">
                <h2 class="text-lg font-bold mb-4">Hourly Forecast</h2>
                <div class="flex flex-col gap-1 items-center py-2 px-3 bg-white/10 backdrop-blur-sm rounded-md"
                 v-for="hour in computedWeatherData.hourly" :key="hour.time">
                    <p class="text-sm">{{ hour.time }}</p>
                    <img class="w-[50px]" :src="hour.icon" :alt="hour.description">
                    <p class="text-sm font-semibold">{{ Math.round(hour.temperature) }}°C</p>
                </div>
            </div>
        </div>
        <div class=" border border-black backdrop-blur-sm bg-white/10">
            <div class="flex flex-col items-center justify-center border border-slate-300 h-40">
                <p class="text-7xl ">{{ Math.round(computedWeatherData.temperature)}}°C</p>
                <p class="text-sm font-semibold"> Feels Like: {{ Math.round(computedWeatherData.feelsLike) }}</p>
            </div>
            <div class="flex flex-col gap-5 items-center h-[500px] overflow-y-auto">
                <h2 class="font-semibold">Daily Forecast</h2>
                <div class="flex flex-row gap-4" v-for="day in computedWeatherData.daily" :key="data.time">
                    <div class=" rounded-md backdrop-blur-sm bg-white/10">
                        <img class="w-[50px]" :src="day.icon" :alt="day.description">
                    </div>
                    <div class="text-start w-40">
                        <p class="text-sm">{{ day.time }}</p>
                        <p class="text-sm">{{ day.description }}</p>
                    </div>
                    <div class="border-l px-2 border-slate-300">
                        <p class="text-sm font-semibold">{{ Math.round(day.maxTemp) }}°</p>
                        <p class="text-sm font-semibold">{{ Math.round(day.minTemp) }}°</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>