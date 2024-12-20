<script setup>

const APIKey = "pk.eyJ1Ijoid2FiaW8taGVuZHJpa2UiLCJhIjoiY20zeWJheDQ3MWZibDJycjMwYzR4MXB2ciJ9.qjd0VqaPNuBPlYvfXHM7YA"

const route =useRoute()
const url = computed(() => {
    return `https://api.openweathermap.org/data/2.5/weather?lat=${city.coord.lat}&lon=${city.coord.lng}&appid=${APIKey}`
})

const {data, execute, status} = useLazyFetch(url, {
    immediate: false
})

const SavedCities = ref([])
const getCities = async () => {
    if (localStorage.getItem('savedCities')) {
        SavedCities.value = JSON.parse(localStorage.getItem('savedCities'))
    }

    const requests = []
    SavedCities.value.forEach((city) => {
        requests.push(
            execute()
        )
    })

    const weatherData = await Promise.all(requests)
}
</script>

<template>
    <div>
        <p>City</p>
    </div>
</template>

<style scoped>

</style>