<script setup>
import { ref, computed } from "vue"


const mapboxAPIKey = "pk.eyJ1Ijoid2FiaW8taGVuZHJpa2UiLCJhIjoiY20zeWJheDQ3MWZibDJycjMwYzR4MXB2ciJ9.qjd0VqaPNuBPlYvfXHM7YA"

// Input query
const searchQuery = ref("")
const results = ref([])
const showNoResultsMessage = ref(false)

// Computed URL with the latest search query
const url = computed(() => {
    return `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
})

// Make API call
const { data, execute, status } = useLazyFetch(url, {
    immediate: false
})

// Fetch and store search results
const getSearchResults = () => {
    if (searchQuery.value.trim() !== "") {
        execute().then(() => {
            // Access features array from the response data
            results.value = data.value?.features || []

            // If no results, show the message and hide it after 3 seconds
            if(results.value.length === 0) {
                showNoResultsMessage.value = true
                setTimeout(() => {
                    showNoResultsMessage.value =false
                }, 2000)
            }
        })
        return
    }
    results.value = null;
    showNoResultsMessage.value = false; // Hide message when input is cleared
}




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
                <div class="bg-gray-100 p-2" v-if="searchQuery">
                    <NuxtLink :to="{ path: `/cityview/${place.place_name.replaceAll(' ', '')}`,
                     query: { lat: place.geometry.coordinates[1], lng: place.geometry.coordinates[0], preview: true } }"
                     v-for="place in results"
                        :key="place.id" class="leading-8 cursor-pointer">
                        <p>{{ place.place_name }}</p>
                    </NuxtLink>
                </div>
            </template>
        </div>
        <CityList/>
    </div>
</template>

<style scoped>

</style>