<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';


</script>

<template>
    <div class="border-b border-slate-200 pb-4 flex items-center flex-col gap-4">
        <div>
            <AnimatedPlaceholder class="w-[100px]" />
        </div>
        <div>
            <AnimatedPlaceholder class="w-[150px]" />
        </div>
        <div>
            <AnimatedPlaceholder class="w-[80px] " />
        </div>
        <div class="text-sm flex flex-col items-center gap-4">
            <div class="flex flex-col gap-2 items-center">
                <div>
                    <AnimatedPlaceholder class="w-[100px]" />
                </div>
                <div>
                    <AnimatedPlaceholder class="w-[70px]" />
                </div>
            </div>
            <div class="flex flex-col gap-2 items-center">
                <div>
                    <AnimatedPlaceholder class="w-[120px]" />
                </div>
                <div>
                    <AnimatedPlaceholder class="w-[60px]" />
                </div>
            </div>
        </div>
        <div class="h-[80px]">
            <AnimatedPlaceholder class="w-[80px] " />
        </div>
        <div>
            <AnimatedPlaceholder class="w-[80px]" />
        </div>
    </div>
</template>

<style scoped>

</style>