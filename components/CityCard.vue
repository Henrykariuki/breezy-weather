<script setup>
defineProps({
    city: {
        type: Object,
        required: true
    }
})

//converting temperature from kelvin to celcius
const tempInCelcius = (city) => city.weather.main.temp - 273.15
const maxTempInCelcius = (city) => city.weather.main.temp_max - 273.15
const minTempInCelcius = (city) => city.weather.main.temp_min - 273.15
</script>

<template>
    <Transition name="fade">
        <div class="text-white p-4 flex justify-between bg-white/20 backdrop-blur-sm rounded-md mt-5">
            <div class="flex items-center">
                <p>{{ city.city }}</p>
            </div>
            <div class="flex flex-col items-end">
                <div class=" text-lg mb-2">
                    <p>{{ Math.round(tempInCelcius(city)) }}°</p>
                </div>
                <div class="flex flex-row gap-2 text-xs">
                    <p>H <span>{{ Math.round(maxTempInCelcius(city)) }}°</span></p>
                    <p>L <span>{{ Math.round(minTempInCelcius(city)) }}°</span></p>
                </div>
            </div>
        </div>
    </Transition>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s ease-in-out;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}
</style>