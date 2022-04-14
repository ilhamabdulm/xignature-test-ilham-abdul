<script setup>
const props = defineProps(['text']);
</script>

<template>
  <button
    class="bg-blue-600 text-white font-semibold h-8 w-40 rounded-md hover:bg-blue-700"
    @click="$emit('action')"
  >
    {{ props.text }}
  </button>
</template>

<style></style>
