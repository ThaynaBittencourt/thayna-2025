<script setup>
import { ref } from "vue";
import Marquee from "./ui/marquee/Marquee.vue";
import ReviewCard from "./ui/marquee/ReviewCard.vue";

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

// Reviews data
const reviews = [
  {
    name: "Roberval - Coord. TI",
    username: "@rober",
    body: "um exelente profissional, super recomendo",
    img: "https://avatar.vercel.sh/jack",
  },
  {
    name: "Joaquim - Coord. Dev",
    username: "@joaquim",
    body: "Thaynã é um excelente funcionario. Sua dedicação e habilidades técnicas são excepcionais.",
    img: "https://avatar.vercel.sh/jill",
  },
  {
    name: "Alan - Tec. nivel 3",
    username: "@alan",
    body: "I'm at a loss for words. This is amazing. I love it.",
    img: "https://avatar.vercel.sh/john",
  },
  {
    name: "Marcilio - Dir. IDEMA",
    username: "@marcilio",
    body: "Um profissional dedicado e competente, sempre disposto a ajudar a equipe.",
    img: "https://avatar.vercel.sh/jane",
  },
  {
    name: "Ramon - Dir. INSCODE",
    username: "@ramon",
    body: "Desde quando entrou na empresa, Thaynã tem se destacado por sua proatividade e habilidades técnicas.",
    img: "https://avatar.vercel.sh/jenny",
  },
  {
    name: "Carol - Postomais",
    username: "@carol",
    body: "Desenvolvedor talentoso, sempre buscando soluções inovadoras, PostoMais é exelente",
    img: "https://avatar.vercel.sh/james",
  },
];

// Split reviews into two rows
const firstRow = ref(reviews.slice(0, reviews.length / 2));
const secondRow = ref(reviews.slice(reviews.length / 2));
</script>

<template>
  <div>
    <div class="container">
      <div class="card z-50">
        <a
          href="https://www.instagram.com/negrescooficial/"
          class="socialContainer containerOne"
        >
          <svg class="socialSvg instagramSvg" viewBox="0 0 16 16">
            <path
              d="M8 0C5.829 0 5.556.01 4.703.048 3.85.088 3.269.222 2.76.42a3.917 3.917 0 0 0-1.417.923A3.927 3.927 0 0 0 .42 2.76C.222 3.268.087 3.85.048 4.7.01 5.555 0 5.827 0 8.001c0 2.172.01 2.444.048 3.297.04.852.174 1.433.372 1.942.205.526.478.972.923 1.417.444.445.89.719 1.416.923.51.198 1.09.333 1.942.372C5.555 15.99 5.827 16 8 16s2.444-.01 3.298-.048c.851-.04 1.434-.174 1.943-.372a3.916 3.916 0 0 0 1.416-.923c.445-.445.718-.891.923-1.417.197-.509.332-1.09.372-1.942C15.99 10.445 16 10.173 16 8s-.01-2.445-.048-3.299c-.04-.851-.175-1.433-.372-1.941a3.926 3.926 0 0 0-.923-1.417A3.911 3.911 0 0 0 13.24.42c-.51-.198-1.092-.333-1.943-.372C10.443.01 10.172 0 7.998 0h.003zm-.717 1.442h.718c2.136 0 2.389.007 3.232.046.78.035 1.204.166 1.486.275.373.145.64.319.92.599.28.28.453.546.598.92.11.281.24.705.275 1.485.039.843.047 1.096.047 3.231s-.008 2.389-.047 3.232c-.035.78-.166 1.203-.275 1.485a2.47 2.47 0 0 1-.599.919c-.28.28-.546.453-.92.598-.28.11-.704.24-1.485.276-.843.038-1.096.047-3.232.047s-2.39-.009-3.233-.047c-.78-.036-1.203-.166-1.485-.276a2.478 2.478 0 0 1-.92-.598 2.48 2.48 0 0 1-.6-.92c-.109-.281-.24-.705-.275-1.485-.038-.843-.046-1.096-.046-3.233 0-2.136.008-2.388.046-3.231.036-.78.166-1.204.276-1.486.145-.373.319-.64.599-.92.28-.28.546-.453.92-.598.282-.11.705-.24 1.485-.276.738-.034 1.024-.044 2.515-.045v.002zm4.988 1.328a.96.96 0 1 0 0 1.92.96.96 0 0 0 0-1.92zm-4.27 1.122a4.109 4.109 0 1 0 0 8.217 4.109 4.109 0 0 0 0-8.217zm0 1.441a2.667 2.667 0 1 1 0 5.334 2.667 2.667 0 0 1 0-5.334z"
            ></path>
          </svg>
        </a>

        <a href="#" class="socialContainer containerTwo">
          <svg class="socialSvg twitterSvg" viewBox="0 0 16 16">
            <path
              d="M5.026 15c6.038 0 9.341-5.003 9.341-9.334 0-.14 0-.282-.006-.422A6.685 6.685 0 0 0 16 3.542a6.658 6.658 0 0 1-1.889.518 3.301 3.301 0 0 0 1.447-1.817 6.533 6.533 0 0 1-2.087.793A3.286 3.286 0 0 0 7.875 6.03a9.325 9.325 0 0 1-6.767-3.429 3.289 3.289 0 0 0 1.018 4.382A3.323 3.323 0 0 1 .64 6.575v.045a3.288 3.288 0 0 0 2.632 3.218 3.203 3.203 0 0 1-.865.115 3.23 3.23 0 0 1-.614-.057 3.283 3.283 0 0 0 3.067 2.277A6.588 6.588 0 0 1 .78 13.58a6.32 6.32 0 0 1-.78-.045A9.344 9.344 0 0 0 5.026 15z"
            ></path>
          </svg>
        </a>

        <a
          href="https://www.linkedin.com/in/thayn%C3%A3-bittencourt-470571274/"
          class="socialContainer containerThree"
        >
          <svg class="socialSvg linkdinSvg" viewBox="0 0 448 512">
            <path
              d="M100.28 448H7.4V148.9h92.88zM53.79 108.1C24.09 108.1 0 83.5 0 53.8a53.79 53.79 0 0 1 107.58 0c0 29.7-24.1 54.3-53.79 54.3zM447.9 448h-92.68V302.4c0-34.7-.7-79.2-48.29-79.2-48.29 0-55.69 37.7-55.69 76.7V448h-92.78V148.9h89.08v40.8h1.3c12.4-23.5 42.69-48.3 87.88-48.3 94 0 111.28 61.9 111.28 142.3V448z"
            ></path>
          </svg>
        </a>

        <a
          href="https://wa.me/5584996839198?text=Ol%C3%A1,%20pode%20me%20ajudar?"
          class="socialContainer containerFour"
        >
          <svg class="socialSvg whatsappSvg" viewBox="0 0 16 16">
            <path
              d="M13.601 2.326A7.854 7.854 0 0 0 7.994 0C3.627 0 .068 3.558.064 7.926c0 1.399.366 2.76 1.057 3.965L0 16l4.204-1.102a7.933 7.933 0 0 0 3.79.965h.004c4.368 0 7.926-3.558 7.93-7.93A7.898 7.898 0 0 0 13.6 2.326zM7.994 14.521a6.573 6.573 0 0 1-3.356-.92l-.24-.144-2.494.654.666-2.433-.156-.251a6.56 6.56 0 0 1-1.007-3.505c0-3.626 2.957-6.584 6.591-6.584a6.56 6.56 0 0 1 4.66 1.931 6.557 6.557 0 0 1 1.928 4.66c-.004 3.639-2.961 6.592-6.592 6.592zm3.615-4.934c-.197-.099-1.17-.578-1.353-.646-.182-.065-.315-.099-.445.099-.133.197-.513.646-.627.775-.114.133-.232.148-.43.05-.197-.1-.836-.308-1.592-.985-.59-.525-.985-1.175-1.103-1.372-.114-.198-.011-.304.088-.403.087-.088.197-.232.296-.346.1-.114.133-.198.198-.33.065-.134.034-.248-.015-.347-.05-.099-.445-1.076-.612-1.47-.16-.389-.323-.335-.445-.34-.114-.007-.247-.007-.38-.007a.729.729 0 0 0-.529.247c-.182.198-.691.677-.691 1.654 0 .977.71 1.916.81 2.049.098.133 1.394 2.132 3.383 2.992.47.205.84.326 1.129.418.475.152.904.129 1.246.08.38-.058 1.171-.48 1.338-.943.164-.464.164-.86.114-.943-.049-.084-.182-.133-.38-.232z"
            ></path>
          </svg>
        </a>
      </div>
      <div>
        <img
          src="../assets/img/eu2.png"
          class="img-mobile"
          alt="Imagem mobile"
        />
        <img
          src="../assets/img/pageBranco.png"
          class="absolute inset-0 w-full h-full object-cover -z-10"
          alt="Background"
          style="height: 65rem"
        />
      </div>
    </div>
    <!-- Grid responsivo -->
    <div class="relative w-full h-full">
      <!-- Imagem de fundo -->
      <img
        src="../assets/img/pageBranco.png"
        class="absolute inset-0 w-full h-full object-cover -z-10"
        alt="Background"
      />

      <!-- Grid de logos -->
      <!-- Grid de logos -->
      <div class="relative mt-6 flex flex-col items-center gap-4 card-icon">
        <!-- Linha 1 -->
        <img src="../assets/img/stacks.png" class="w-90" />
        <div class="grid grid-cols-3 gap-5">
          <a
            v-for="(logo, index) in logos.slice(0, 3)"
            :key="index"
            @mouseover="activateLogo(index)"
            @mouseleave="deactivateLogo(index)"
            @click="toggleLogo(index)"
            class="cursor-pointer"
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

        <!-- Linha 2 -->
        <div class="grid grid-cols-3">
          <a
            v-for="(logo, index) in logos.slice(3, 6)"
            :key="index"
            @mouseover="activateLogo(index + 3)"
            @mouseleave="deactivateLogo(index + 3)"
            @click="toggleLogo(index + 3)"
            class="cursor-pointer"
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

        <!-- Linha 3 -->
        <div class="grid grid-cols-2">
          <a
            v-for="(logo, index) in logos.slice(6, 8)"
            :key="index"
            @mouseover="activateLogo(index + 6)"
            @mouseleave="deactivateLogo(index + 6)"
            @click="toggleLogo(index + 6)"
            class="cursor-pointer"
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
      </div>
    </div>

    <!-- Imagem de fundo -->
    <div class="relative w-screen h-screen">
      <img
        src="../assets/img/pageBranco.png"
        class="absolute inset-0 w-full h-full object-cover -z-10"
        alt="Background"
        style="margin-top: -9rem"
      />

      <!-- Conteúdo por cima da imagem -->
      <div class="absolute inset-0 flex flex-col items-center justify-center">
        <img
          src="../assets/img/projetos.png"
          class="w-90"
          style="margin-bottom: 40px; margin-top: 450px"
        />

        <div
          class="grid grid-cols-1 sm:grid-cols-4 md:grid-cols-5 gap-10 mt-6 text-center"
        >
          <!-- item 1 -->
          <div class="flex flex-col gap-3 items-center">
            <img
              src="../assets/img/detranPb.png"
              class="w-90 bg-shadow transition-transform duration-300 hover:scale-110 rounded-md"
            />
            <button>DETRAN-PB</button>
          </div>

          <!-- item 2 -->
          <div class="flex flex-col gap-3 items-center">
            <img
              src="../assets/img/postomais.png"
              class="w-90 transition-transform duration-300 hover:scale-110 rounded-md bg-shadow"
            />
            <button>POSTO+</button>
          </div>

          <!-- item 3 -->
          <div class="flex flex-col gap-3 items-center">
            <img
              src="../assets/img/idema.png"
              class="w-90 transition-transform duration-300 hover:scale-110 rounded-md bg-shadow"
            />
            <button>IDEMA</button>
          </div>

          <!-- item 4 -->
          <div class="flex flex-col gap-3 items-center">
            <img
              src="../assets/img/sansil2.png"
              class="w-90 transition-transform duration-300 hover:scale-110 rounded-md bg-shadow"
            />
            <button>SANSIL</button>
          </div>
        </div>
      </div>
    </div>
    <!-- Imagem de fundo -->
    <div class="relative w-screen h-screen">
      <img
        src="../assets/img/pageBranco.png"
        class="absolute inset-0 w-full h-full object-cover -z-10"
        alt="Background"
        style="margin-top: -9rem"
      />

      <!-- Conteúdo por cima da imagem -->
      <!-- <div
        class="absolute inset-0 flex flex-col items-center justify-center"
      ></div> -->
    </div>

    <div class="relative w-screen h-50">
      <img
        src="../assets/img/pageBranco.png"
        class="absolute inset-0 w-full h-full object-cover -z-10"
        alt="Background"
      />
      <img
        src="../assets/img/pageBranco.png"
        class="absolute inset-0 w-full h-full object-cover -z-10"
        alt="Background"
        style="margin-top: -10rem"
      />

      <!-- Conteúdo por cima da imagem -->
      <div
        class="absolute inset-0 flex flex-col items-center justify-center"
        style="margin-top: -10rem"
      >
        <div
          class="relative flex w-full flex-col items-center justify-center overflow-hidden"
        >
          <!-- First Marquee -->
          <Marquee pause-on-hover class="[--duration:20s]">
            <ReviewCard
              v-for="review in firstRow"
              :key="review.username"
              :img="review.img"
              :name="review.name"
              :username="review.username"
              :body="review.body"
            />
          </Marquee>

          <!-- Second Marquee (reverse) -->
          <Marquee reverse pause-on-hover class="[--duration:20s]">
            <ReviewCard
              v-for="review in secondRow"
              :key="review.username"
              :img="review.img"
              :name="review.name"
              :username="review.username"
              :body="review.body"
            />
          </Marquee>

          <!-- Left Gradient -->
          <div
            class="pointer-events-none absolute inset-y-0 left-0 w-1/3 dark:from-background"
          ></div>

          <!-- Right Gradient -->
          <div
            class="pointer-events-none absolute inset-y-0 right-0 w-1/3 dark:from-background"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Estilo padrão (mobile first) */

/* 
    TESTE
*/
button {
  --bg: #30b28d;
  --text-color: #fff;
  position: relative;
  width: 350px;
  border: none;
  background: var(--bg);
  color: var(--text-color);
  padding: 1em;
  font-weight: bold;
  text-transform: uppercase;
  transition: 0.2s;
  border-radius: 5px;
  opacity: 0.8;
  letter-spacing: 1px;
  box-shadow: #228065 0px 7px 2px, #000 0px 8px 5px;
  margin-top: 15px;
}

button:hover {
  opacity: 1;
}

button:active {
  top: 4px;
  box-shadow: #228065 0px 3px 2px, #000 0px 3px 5px;
}
/* 
    TESTE
*/

.bg-shadow {
  border-radius: 20px;

  box-shadow: 15px 15px 30px #bebebe, -15px -15px 30px #ffffff;
}

.img-mobile {
  width: 100%;
  margin-left: 0px;
  margin-top: 50px;
  margin-bottom: 30px;
}
.logo {
  height: 6em;
  padding: 1em;
  transition: filter 0.3s, transform 0.3s;
}

.grayscale {
  filter: grayscale(100%);
}
.grayscale-0 {
  filter: grayscale(0%);
}
/* From Uiverse.io by vinodjangid07 */
.container {
  width: 100vw;
  height: 63rem;
  display: flex;
  flex-direction: column;
  justify-content: center; /* Centraliza horizontalmente */
  align-items: center; /* Centraliza verticalmente */
}

.card-icon {
  width: 100vw;
  height: 46rem;
  display: flex;
  justify-content: center;
  margin-top: -20rem;
  margin-bottom: 8rem;
}

.card {
  width: fit-content;
  height: fit-content;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 80%;
  gap: 5px;
  box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.055);
  /* Remover margens que atrapalham a centralização */
  margin: 0;
  margin-top: -16rem;
}

/* for all social containers*/
.socialContainer {
  width: 42px;
  height: 42px;
  background-color: #26765f;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  transition-duration: 0.3s;
  border-radius: 10px;
}
/* instagram*/
.containerOne:hover {
  background-color: #128c7e;
  transition-duration: 0.3s;
}
/* twitter*/
.containerTwo:hover {
  background-color: #128c7e;
  transition-duration: 0.3s;
}
/* linkdin*/
.containerThree:hover {
  background-color: #128c7e;
  transition-duration: 0.3s;
}
/* Whatsapp*/
.containerFour:hover {
  background-color: #128c7e;
  transition-duration: 0.3s;
}

.socialContainer:active {
  transform: scale(0.9);
  transition-duration: 0.3s;
}

.socialSvg {
  width: 17px;
}

.socialSvg path {
  fill: rgb(255, 255, 255);
}

.socialContainer:hover .socialSvg {
  animation: slide-in-top 0.3s both;
}

@keyframes slide-in-top {
  0% {
    transform: translateY(-50px);
    opacity: 0;
  }

  100% {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>
