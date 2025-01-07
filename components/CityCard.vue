<script setup>
defineProps({
    city: {
        type: Object,
        required: true
    }
})
</script>

<template>
    <div class="text-white p-4 flex justify-between w-1/2 border border-black mt-5">
        <p>{{ city.city }}</p>
        <div class="flex flex-row gap-2">
            <p>H-<span>{{ city.weather.main.temp_max }}</span></p>
            <p>L-<span>{{ city.weather.main.temp_min }}</span></p>
        </div>
    </div>
</template>

<style scoped>

</style>