<template>
  <div class="flex justify-center cursor-pointer flex-wrap gap-4">
    <a
      v-for="(logo, index) in logos"
      :key="index"
      @mouseover="activateLogo(index)"
      @mouseleave="deactivateLogo(index)"
      @click="toggleLogo(index)"
    >
      <img
        :src="logo.src"
        :alt="logo.alt"
        class="logo transition duration-300"
        :class="{
          grayscale: !logo.active,
          'grayscale-0': logo.active,
        }"
      />
    </a>
  </div>
</template>

<script setup>
import { ref } from "vue";

import img2 from "../assets/img/2.png";
import img3 from "../assets/img/3.png";
import img4 from "../assets/img/4.png";
import img5 from "../assets/img/5.png";
import img6 from "../assets/img/6.png";
import img7 from "../assets/img/7.png";
import img8 from "../assets/img/8.png";
import imgN8n from "../assets/img/n8n-logo.png";

const logos = ref([
  { src: img2, alt: "Vite logo", active: false },
  { src: img3, alt: "Vue logo", active: false },
  { src: img4, alt: "Vite logo", active: false },
  { src: img5, alt: "Vue logo", active: false },
  { src: img6, alt: "Vite logo", active: false },
  { src: img7, alt: "Vue logo", active: false },
  { src: img8, alt: "Vite logo", active: false },
  { src: imgN8n, alt: "N8N logo", active: false },
]);

// Hover acende
const activateLogo = (index) => {
  logos.value[index].active = true;
};

// Saiu do hover, apaga (se não foi clicado)
const deactivateLogo = (index) => {
  if (!logos.value[index].clicked) {
    logos.value[index].active = false;
  }
};

// Click fixa o estado
const toggleLogo = (index) => {
  const logo = logos.value[index];
  logo.clicked = !logo.clicked;
  logo.active = logo.clicked;
};
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  transition: filter 0.3s, transform 0.3s;
}

.grayscale {
  filter: grayscale(100%);
}
.grayscale-0 {
  filter: grayscale(0%);
}
</style>
