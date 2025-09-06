<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import HomeWeb from "./HomeWeb.vue";
import HomeMobile from "./HomeMobile.vue";

const isMobile = ref(false);

const checkScreen = () => {
  isMobile.value = window.innerWidth <= 768; // breakpoint
};

onMounted(() => {
  checkScreen();
  window.addEventListener("resize", checkScreen);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", checkScreen);
});
</script>

<template>
  <component :is="isMobile ? HomeMobile : HomeWeb" />
</template>
