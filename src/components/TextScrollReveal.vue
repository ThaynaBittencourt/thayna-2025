<template>
  <div
    :class="['relative z-0 h-[200vh]', props.class]"
    ref="textScrollRevealRef"
  >
    <div
      class="sticky top-0 mx-auto flex h-1/2 max-w-4xl items-center bg-transparent px-4 py-20"
    >
      <p
        class="flex flex-nowrap whitespace-nowrap p-5 text-2xl font-bold text-white"
      >
        <slot
          v-for="(word, i) in words"
          :key="i"
          :word="word"
          :progress="scrollYProgress"
          :range="[i / words.length, (i + 1) / words.length]"
        >
          <!-- fallback padrão caso não passe slot -->
          <span class="mx-1">{{ word }}</span>
        </slot>
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from "vue";

interface Props {
  class?: string;
  text: string;
}

const props = defineProps<Props>();

const textScrollRevealRef = ref<HTMLElement | null>(null);
const words = computed(() => props.text.split(" "));
const scrollYProgress = ref(0);

function updateScrollYProgress() {
  if (textScrollRevealRef.value) {
    const boundingRect = textScrollRevealRef.value.getBoundingClientRect();
    const windowHeight = window.innerHeight;
    scrollYProgress.value = (boundingRect.y / windowHeight) * -1;
  }
}

onMounted(() => {
  window.addEventListener("scroll", updateScrollYProgress);
  window.addEventListener("resize", updateScrollYProgress);
  updateScrollYProgress();
});

onUnmounted(() => {
  window.removeEventListener("scroll", updateScrollYProgress);
  window.removeEventListener("resize", updateScrollYProgress);
});
</script>
