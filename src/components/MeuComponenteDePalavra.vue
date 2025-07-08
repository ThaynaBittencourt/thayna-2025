<template>
  <span class="relative mx-1 whitespace-nowrap">
    <span
      class="absolute opacity-30 dark:opacity-60 select-none pointer-events-none"
    >
      {{ word }}
    </span>
    <span :style="{ opacity: computedOpacity }" class="text-white">
      {{ word }}&nbsp;
    </span>
  </span>
</template>

<script setup lang="ts">
import { computed } from "vue";

interface Props {
  word: string;
  progress: number;
  range: [number, number];
}

const props = defineProps<Props>();

const computedOpacity = computed(() => {
  const [start, end] = props.range;
  const progress = props.progress;

  if (progress < start) return 0;
  if (progress > end) return 1;

  return (progress - start) / (end - start);
});
</script>
