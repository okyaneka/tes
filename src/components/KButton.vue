<script setup>
const props = defineProps({
  text: {
    type: String,
    default: "Text",
  },
  outlined: Boolean,
});
</script>

<template>
  <button
    class="rounded-md px-4 py-1"
    :class="
      outlined
        ? 'text-green-700 border border-green-700 hover:bg-green-50'
        : 'text-white bg-green-600 hover:bg-green-700'
    "
  >
    {{ props.text }}
  </button>
</template>
