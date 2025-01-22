<script setup>
import { Sun, CircleAlert, Plus } from 'lucide-vue-next';
import { uid } from 'uid';


const route = useRoute()
const router = useRouter()
const modalActive = ref(null)
const toggleModule = () => {
    modalActive.value = !modalActive.value
}

const savedCities = ref([])

const addCity = () => {
    if (import.meta.client && localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
    }

    const locationInfo = {
        id: uid(),
        city: route.params.id,
        coords: {
            lat: route.query.lat,
            lng: route.query.lng
        }
    }

    savedCities.value.push(locationInfo)
    localStorage.setItem('savedCities', JSON.stringify(savedCities.value))

    let query = Object.assign({}, route.query)
    delete query.preview
    query.id = locationInfo.id
    router.replace({query})
}

</script>

<template>
    <div class="text-white flex justify-around py-4 relative  bg-black"
        style="box-shadow: 0 4px 10px rgba(0, 0, 0, 0.8);">
        <div class="flex flex-row gap-2">
            <Sun />
            <NuxtLink to="/">Breezy Weather</NuxtLink>
        </div>
        <div class="flex flex-row gap-2">
            <div @click="toggleModule">
                <CircleAlert />
            </div>
            <div v-if="route.query.preview" @click="addCity">
                <Plus />
            </div>
        </div>
        <div class="absolute rounded-sm top-32 text-black z-50 bg-white/20 backdrop-blur-sm">
            <AboutModal :modalActive="modalActive" @closeModal="toggleModule">
                <div>
                    <div>
                        <p class="text-lg font-bold">About:</p>
                        <p>The Breezy Weather allows you to track the current and future weather of cities of your
                            choosing.</p>
                    </div>
                    <div class="my-4">
                        <p class="text-lg font-bold">How it works:</p>
                        <p>Search for your city by entering the name into the search bar.</p>
                        <p>Select a city within the result, this will take you to the current weather for your
                            selection.</p>
                        <p>Track the city by clicking on the '+' icon in the top right. This will save the city to view
                            at a later time on the home page.</p>
                    </div>
                    <div>
                        <p class="text-lg font-bold">Removing a city:</p>
                        <p>If you no longer wish to track a city, simply select the city within the home page. At the
                            bottom of the page, there will be an option to delete the city.</p>
                    </div>
                </div>
            </AboutModal>
        </div>
    </div>
</template>

<style scoped></style>