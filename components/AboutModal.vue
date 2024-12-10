<script setup>
defineEmits(['closeModal'])
defineProps({
    modalActive: {
        type: Boolean,
        default: false
    }
})
</script>

<template>
    <Transition name="modal-outer">
        <div class="flex flex-col gap-4 max-w-screen-md p-4" v-show="modalActive">
            <Transition name="modal-inner">
                <div v-if="modalActive">
                    <slot />
                    <button class="bg-[#808080] py-1 px-4 text-white mt-4 rounded-sm" @click="$emit('closeModal')">
                        Close
                    </button>
                </div>
            </Transition>
        </div>
    </Transition>
</template>

<style scoped>
.modal-outer-enter-active,
.modal-outer-leave-active {
    transition: opacity 0.5s ease, transform 0.3s ease;
}

.modal-outer-enter-from,
.modal-outer-leave-to {
    opacity: 0;
}

.modal-inner-enter-active {
    transition: opacity 0.5s ease, transform 0.15s ease;
}

.modal-inner-leave-active {
    transition: opacity 0.5s ease, transform 0.5s ease;
}

.modal-inner-enter-from {
    opacity: 0;
    transform: scale(0.8);
}

.modal-inner-leave-to {
    opacity: 0;
}
</style>