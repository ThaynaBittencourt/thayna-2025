<template>
  <div class="bg-[#181818]">
    <!-- TITULO -->
    <div class="flex h-80 items-center justify-center">
      <SparklesText
        text="Loris Hue"
        :colors="{ first: '#fde047', second: '#65a30d' }"
        :sparkles-count="5"
        class="my-8"
      />
    </div>
    <!-- GALERIA RESPONSIVA -->
    <ExpandableGallery :images="images" class="p-4" />
    <!-- LUPA ESPECIAL (MODIFICAR) -->
    <div
      class="relative mx-auto my-10 w-full max-w-md overflow-hidden rounded-3xl bg-gradient-to-r from-[#1D2235] to-[#121318] p-8"
    >
      <Rays />
      <Beams />
      <div class="relative z-10">
        <Lens :hovering="hovering" @hover-update="setHovering">
          <img
            src="https://images.unsplash.com/photo-1541701494587-cb58502866ab?q=80&w=3870&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
            alt="image"
            width="500"
            height="500"
            class="rounded-2xl"
          />
        </Lens>
        <div
          :style="{ filter: hovering ? 'blur(2px)' : 'blur(0px)' }"
          class="relative z-20 py-4"
        >
          <h2 class="text-left text-2xl font-bold text-white">
            Apple Vision Pro
          </h2>
          <p class="mt-4 text-left text-neutral-200">
            The all new apple vision pro was the best thing that happened around
            8 months ago, not anymore.
          </p>
        </div>
      </div>
    </div>

    <div class="z-10 flex min-h-64 items-center justify-center dark:bg-black">
      <TextScrollReveal text="Alguns comentários dos nossos clientes">
        <template #default="{ word, progress, range }">
          <MeuComponenteDePalavra
            :word="word"
            :progress="progress"
            :range="range"
          />
        </template>
      </TextScrollReveal>
    </div>

    <!-- COMENTARIOS ROLANTES -->
    <div
      class="relative flex h-[500px] w-full flex-col items-center justify-center overflow-hidden"
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
        class="pointer-events-none absolute inset-y-0 left-0 w-1/3 bg-gradient-to-r"
      ></div>

      <!-- Right Gradient -->
      <div
        class="pointer-events-none absolute inset-y-0 right-0 w-1/3 bg-gradient-to-l"
      ></div>
    </div>
  </div>

  <!-- MAIS IDEISAS A BAIXO -->
</template>

<script setup>
import Lens from "./components/ui/lens/Lens.vue";
import SparklesText from "./components/ui/sparkles-text/SparklesText.vue";
import ExpandableGallery from "./components/ui/expandable-gallery/ExpandableGallery.vue";
import { ref } from "vue";
import Marquee from "./components/ui/marquee/Marquee.vue";
import ReviewCard from "./components/ui/marquee/ReviewCard.vue";
import TextScrollReveal from "./components/TextScrollReveal.vue";
import MeuComponenteDePalavra from "./components/MeuComponenteDePalavra.vue";

// Reviews data
const reviews = [
  {
    name: "Lucas",
    username: "@lucas",
    body: "O trabalho com resina está simplesmente impecável, cada detalhe demonstra muita habilidade e paixão!",
    img: "https://avatar.vercel.sh/lucas",
  },
  {
    name: "Mariana",
    username: "@mariana",
    body: "Fiquei encantada com a delicadeza das peças. É arte que transforma e emociona!",
    img: "https://avatar.vercel.sh/mariana",
  },
  {
    name: "Carlos",
    username: "@carlos",
    body: "A criatividade e o acabamento das peças de resina são de outro nível. Parabéns pelo talento!",
    img: "https://avatar.vercel.sh/carlos",
  },
  {
    name: "Fernanda",
    username: "@fernanda",
    body: "Cada peça é uma verdadeira obra de arte. Admiro muito o cuidado e a técnica envolvidos!",
    img: "https://avatar.vercel.sh/fernanda",
  },
  {
    name: "Pedro",
    username: "@pedro",
    body: "Surpreendente como a resina ganha vida nas mãos do artista. Trabalho excepcional!",
    img: "https://avatar.vercel.sh/pedro",
  },
  {
    name: "Aline",
    username: "@aline",
    body: "O acabamento perfeito e a originalidade das peças mostram um talento raro. Parabéns!",
    img: "https://avatar.vercel.sh/aline",
  },
];

// Split reviews into two rows
const firstRow = ref(reviews.slice(0, reviews.length / 2));
const secondRow = ref(reviews.slice(reviews.length / 2));

const images = [
  "https://images.unsplash.com/photo-1541701494587-cb58502866ab?q=80&w=3870&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
  "https://images.unsplash.com/photo-1541701494587-cb58502866ab?q=80&w=3870&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
  "https://images.unsplash.com/photo-1541701494587-cb58502866ab?q=80&w=3870&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
  "https://images.unsplash.com/photo-1541701494587-cb58502866ab?q=80&w=3870&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D",
];
</script>
