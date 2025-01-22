<script setup>

</script>

<template>
  <div >
    <NuxtLoadingIndicator />
    <NuxtLayout>
      <Transition name="page">
        <NuxtPage />
      </Transition>
    </NuxtLayout>
  </div>
</template>

<style scoped>
.page-enter-active,
.page-leave-active {
  transition: 600ms ease all;
}

.page-enter-from,
.page-leave-from {
  opacity: 0;
}
</style>