<script setup>
import { AspectRatio } from "reka-ui";

const props = defineProps({
  ratio: { type: Number, required: false },
  asChild: { type: Boolean, required: false },
  as: { type: [String, Object, Function], required: false },
});
</script>

<template>
  <AspectRatio data-slot="aspect-ratio" v-bind="props">
    <slot />
  </AspectRatio>
</template>
