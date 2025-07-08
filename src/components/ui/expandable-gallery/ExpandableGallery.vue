<template>
  <div :class="cn('w-full gap-2', props.class, layoutClass)">
    <div
      v-for="(image, index) in images"
      :key="image"
      class="relative cursor-pointer overflow-hidden rounded-xl transition-all duration-500 ease-in-out"
      :class="[
        isMobile
          ? 'w-full h-48'
          : index === activeIndex
          ? 'flex-[3]'
          : 'flex-1',
        baseImageClass,
      ]"
      @click="isMobile ? (activeIndex = index) : null"
      @mouseover="!isMobile && (activeIndex = index)"
    >
      <img
        class="object-cover w-full h-full"
        :src="image"
        :alt="'Image ' + index"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { cn } from "@/lib/utils";

const props = defineProps({
  images: { type: Array, required: true },
  class: { type: null, required: false },
});

const activeIndex = ref(0);
const isMobile = ref(false);

// Detecta se está no mobile
onMounted(() => {
  const check = () => {
    isMobile.value = window.innerWidth <= 768;
  };
  check();
  window.addEventListener("resize", check);
});

// Classes responsivas
const layoutClass = computed(() =>
  isMobile.value ? "flex flex-col" : "flex h-96"
);
const baseImageClass = "transition-all";
</script>
