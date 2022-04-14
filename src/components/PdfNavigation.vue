<script setup>
import Button from './atoms/Button.vue';

const props = defineProps(['currentPage', 'totalPages']);
</script>

<template>
  <header class="w-full flex items-center justify-center gap-3 mb-6">
    <Button text="Prev" @action="$emit('onPrev')" />
    <div>
      <span>Page {{ props.currentPage }} / {{ props.totalPages }} </span>
    </div>
    <Button text="Next" @action="$emit('onNext')" />
  </header>
</template>

<style></style>
