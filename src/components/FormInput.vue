<script setup>
const props = defineProps({
  type: { type: String, default: "text" },
});
</script>

<template>
  <input
    :type="props.type"
    class="rounded-md relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-green-500 focus:border-green-500 focus:z-10 sm:text-sm"
  />
</template>
