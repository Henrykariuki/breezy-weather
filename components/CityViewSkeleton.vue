<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';


</script>

<template>
    <div>
        <div class=" grid grid-cols-1 md:grid-cols-4 py-5 p md:pr-5">
            <div class="col-span-3 px-5 gap-12 md:gap-0 flex flex-col justify-end ">
                <div class=" md:hidden flex flex-col gap-2  justify-center  mb-5 h-40">
                    <div class="flex justify-between items-center">
                        <AnimatedPlaceholder class="w-[120px]" />
                        <AnimatedPlaceholder class="w-[60px]" />
                    </div>
                    <AnimatedPlaceholder class="w-[20px]" />
                    <AnimatedPlaceholder class="w-[20px]" />
                </div>
                <p class="bg-clip-text text-transparent bg-gradient-to-l from-slate-500 to-yellow-500 
                    md:text-right md:mb-10 text-8xl font-semibold">
                    <AnimatedPlaceholder class="w-[200px]" />
                </p>
                <div class="scrollable-container py-6 border-t border-slate-300 flex flex-row gap-4 overflow-x-auto">
                    <div class="flex flex-col gap-1 items-center py-2 px-3 bg-white/10 backdrop-blur-sm rounded-md"
                        v-for="i in 12">
                        <AnimatedPlaceholder class="w-[20px]" />
                        <AnimatedPlaceholder class="w-[50px]" />
                        <AnimatedPlaceholder class="w-[20px]" />
                    </div>
                </div>
                <div  class=" hidden md:block mb-4 text-lg duration-150 text-center mt-5 p-2 w-48 
                    bg-gradient-to-r from-slate-500 to-green-500 
                    hover:from-red-500 hover:to-yellow-500 rounded-md">
                    <AnimatedPlaceholder class="w-[50px]" />
                </div>
            </div>
            <div class="md:px-5 backdrop-blur-sm bg-white/10 rounded-md">
                <div
                    class="hidden md:block flex flex-col gap-2 items-center justify-center border-b border-slate-300 mb-5 h-40">
                    <AnimatedPlaceholder class="w-[100px]" />
                    <AnimatedPlaceholder class="w-[200px]" />
                    <AnimatedPlaceholder class="w-[20px]" />
                </div>
                <div
                    class="scrollable-container flex flex-col gap-5 mt-20 md:mt-0 lg:items-center px-5 lg:px-0 h-[500px] overflow-y-auto">
                    <AnimatedPlaceholder class="w-[100px]" />
                    <div class="flex flex-row gap-4" v-for="i in 7">
                        <div class=" rounded-md backdrop-blur-sm bg-white/10">
                            <AnimatedPlaceholder class="w-[50px]" />
                        </div>
                        <div class="text-start">
                            <AnimatedPlaceholder class="w-[100px]" />
                            <AnimatedPlaceholder class="w-[50px]" />
                        </div>
                        <div class="border-l flex flex-col justify-between px-2 border-slate-300">
                            <AnimatedPlaceholder class="w-[20px]" />
                            <AnimatedPlaceholder class="w-[20px]" />
                        </div>
                    </div>
                </div>
                <div class=" md:hidden ml-5 mb-4 text-lg duration-150 text-center mt-5 p-2  rounded-md">
                    <AnimatedPlaceholder class="w-[50px]" />
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>

</style>