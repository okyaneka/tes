<template>
  <div
    class="p-4 flex items-center gap-4 rounded-md border border-green-200 hover:bg-green-50"
  >
    <i class="block text-green-700">
      <slot name="icon"></slot>
    </i>
    <div class="">
      <div class="text-xl font-semibold text-green-700">
        <slot name="heading"></slot>
      </div>
      <slot></slot>
    </div>
  </div>
</template>
