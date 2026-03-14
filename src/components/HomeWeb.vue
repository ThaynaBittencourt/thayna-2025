<!-- eslint-disable vue/block-lang -->
<script setup>
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { ArrowUpRight } from 'lucide-vue-next';
import { onMounted, onUnmounted, ref, nextTick } from 'vue';

gsap.registerPlugin(ScrollTrigger);

// Refs de estado
const percentage = ref(0);
const isLoading = ref(true);

const projects = [
  {
    title: 'INTEGRAÇÃO COM IA',
    category: 'Inteligência Artificial',
    image:
      'https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1000&auto=format&fit=crop',
  },
  {
    title: 'TESTE',
    category: 'Workflow',
    image:
      'https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1000&auto=format&fit=crop',
  },
  {
    title: 'Meus projetos',
    category: 'vamos criar juntos?',
    image:
      'https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1000&auto=format&fit=crop',
  },
];

onMounted(() => {
  const tlLoader = gsap.timeline({
    defaults: { ease: 'power2.inOut' },
  });

  const counter = { value: 0 };

  // 1. Sequência do Loader
  tlLoader
    .to(counter, {
      value: 100,
      duration: 2,
      onUpdate: () => {
        percentage.value = Math.floor(counter.value);
      },
    })
    .to('.loader-container', {
      yPercent: -100,
      duration: 1.2,
      delay: 0.2,
    })
    .add(() => {
      // Remove o estado de loading e limpa triggers fantasmas
      isLoading.value = false;
      ScrollTrigger.getAll().forEach((t) => t.kill());

      // Aguarda o Vue atualizar o DOM após remover o loader
      nextTick().then(() => {
        iniciarAnimacoesPrincipais();
      });
    });
});

function iniciarAnimacoesPrincipais() {
  // Revelação do Header
  gsap.to('.reveal-header', {
    opacity: 1,
    y: 0,
    stagger: 0.1,
    skewY: 0,
    duration: 1.5,
    ease: 'power4.out',
  });

  // Revelação das Imagens (Clip Path)
  gsap.to('.image-reveal', {
    clipPath: 'polygon(0% 0%, 100% 0%, 100% 100%, 0% 100%)',
    stagger: 0.2,
    duration: 1.5,
    ease: 'expo.out',
  });

  // --- CONFIGURAÇÃO DO SCROLL HORIZONTAL ---
  const slider = document.querySelector('.div-horizontal');
  const sections = gsap.utils.toArray('.secao-horizontal');

  if (slider) {
    const sliderTl = gsap.timeline({
      scrollTrigger: {
        trigger: slider,
        pin: true,
        scrub: 1,
        snap: 1 / (sections.length - 1),
        start: 'top top',
        end: () => '+=' + (slider.scrollWidth - window.innerWidth),
        invalidateOnRefresh: true,
      },
    });

    sliderTl.to(sections, {
      xPercent: -100 * (sections.length - 1),
      ease: 'none',
    });
  }

  // Parallax das imagens
  gsap.utils.toArray('.parallax-img').forEach((img) => {
    gsap.to(img, {
      yPercent: 20,
      ease: 'none',
      scrollTrigger: {
        trigger: img,
        scrub: true,
      },
    });
  });

  // Efeito de flutuação contínuo (loop)
  gsap.to('.login-bounce', {
    y: '-=15',
    duration: 2.5,
    ease: 'power1.inOut',
    yoyo: true,
    repeat: -1,
  });

  ScrollTrigger.refresh();
}

onUnmounted(() => {
  ScrollTrigger.getAll().forEach((t) => t.kill());
});
</script>

<template>
  <div
    class="noise-container min-h-screen bg-[#F9F9F7] text-black selection:bg-black selection:text-white"
  >
    <section
      class="relative flex min-h-screen items-center overflow-hidden px-8 lg:px-20"
    >
      <div class="max-w-[1400px]">
        <div
          class="grid w-full max-w-[1600px] grid-cols-1 items-center gap-16 lg:grid-cols-2"
        >
          <div class="z-10 order-2 lg:order-1">
            <div class="mb-4 overflow-hidden">
              <span
                class="reveal-text inline-block translate-y-full text-xs font-bold tracking-widest text-emerald-600 uppercase"
              >
                Based in Natal, RN / Full Stack Developer
              </span>
            </div>
            <div class="flex flex-col justify-center">
              <div>
                <h1
                  class="reveal-header font-ubermove-bold translate-y-[100px] skew-y-4 text-[12vw] leading-[0.85] font-bold tracking-tighter uppercase opacity-0 lg:text-[5vw]"
                >
                  Thaynã <br />
                  <span class="text-[6vw] text-gray-400">Bittencourt</span>
                </h1>
              </div>
              <div>
                <p
                  class="font-ubermove mt-6 max-w-md skew-y-4 text-xl text-zinc-600"
                >
                  Desenvolvedor Full Stack especializado em ecossistema Laravel,
                  Vue.js e automações com n8n.
                </p>
              </div>
            </div>

            <div class="reveal-text mt-10 flex space-x-6 opacity-0">
              <Github
                class="h-5 w-5 cursor-pointer transition-colors hover:text-emerald-600"
              />
              <Linkedin
                class="h-5 w-5 cursor-pointer transition-colors hover:text-emerald-600"
              />
              <Mail
                class="h-5 w-5 cursor-pointer transition-colors hover:text-emerald-600"
              />
            </div>
          </div>

          <div class="relative order-1 lg:order-2">
            <div
              class="hero-img-wrapper aspect-4/5 overflow-hidden rounded-2xl bg-zinc-200 lg:aspect-square"
            >
              <img
                src="/img/eu preto e branco.jpeg"
                alt="Thaynã"
                class="hero-img h-full w-full scale-110 object-cover object-[center_38%] grayscale"
              />
            </div>
          </div>
        </div>
      </div>
    </section>

    <section
      class="div-horizontal flex h-screen w-[300vw] flex-nowrap overflow-x-hidden bg-black text-white"
    >
      <div
        class="secao-horizontal flex h-full w-screen items-center justify-center border-r border-white/10"
      >
        <h1 class="font-ubermove text-[10vw] font-bold uppercase">Agilidade</h1>
      </div>
      <div
        class="secao-horizontal flex h-full w-screen items-center justify-center border-r border-white/10 bg-emerald-950"
      >
        <h1 class="font-ubermove text-[10vw] font-bold uppercase">
          Modernidade
        </h1>
      </div>
      <div
        class="secao-horizontal flex h-full w-screen items-center justify-center bg-zinc-900"
      >
        <h1 class="font-ubermove text-[10vw] font-bold uppercase">Inovação</h1>
      </div>
    </section>

    <section class="bg-black px-8 py-30 text-white lg:px-20">
      <div
        class="mb-20 flex flex-col py-15 items-end justify-between gap-8 md:flex-row"
      >
        <h2 class="font-ubermove-bold text-5xl tracking-tighter uppercase">
          Selected <br />
          <span class="text-zinc-600">Works</span>
        </h2>
        <p class="max-w-xs text-sm text-zinc-400 italic">
          Projetos desenvolvidos com foco em performance, escalabilidade e
          experiência do usuário.
        </p>
      </div>

      <div class="grid grid-cols-1 gap-20 lg:grid-cols-3">
        <div
          v-for="(project, idx) in projects"
          :key="idx"
          class="group cursor-pointer"
        >
          <div
            class="relative aspect-[3/4] overflow-hidden rounded-xl bg-zinc-900"
          >
            <img
              :src="project.image"
              class="h-full w-full object-cover opacity-50 grayscale transition-all duration-700 group-hover:scale-105 group-hover:opacity-100 group-hover:grayscale-0"
            />
            <div
              class="absolute top-6 right-6 rounded-full border border-white/20 bg-black/20 p-4 opacity-0 backdrop-blur-md transition-opacity group-hover:opacity-100"
            >
              <ArrowUpRight class="h-6 w-6" />
            </div>
          </div>
          <div class="mt-8 flex items-start justify-between">
            <div>
              <p
                class="mb-2 text-[10px] font-bold tracking-widest text-emerald-500 uppercase"
              >
                {{ project.category }}
              </p>
              <h3 class="font-ubermove-bold text-3xl tracking-tighter">
                {{ project.title }}
              </h3>
            </div>
            <span class="font-mono text-sm text-zinc-600">{{
              project.year
            }}</span>
          </div>
        </div>
      </div>
    </section>

    <section class="flex h-screen items-center justify-center bg-[#F9F9F7]">
      <h2
        translate="no"
        class="font-ubermove text-[15vw] font-bold tracking-tighter text-black uppercase opacity-5"
      >
        developer
      </h2>
    </section>

    <div
      v-if="isLoading"
      class="loader-container fixed inset-0 z-[100] flex flex-col items-center justify-center bg-black text-white"
    >
      <div class="text-center">
        <p
          class="font-ubermove mb-8 text-[10px] tracking-[0.8em] uppercase opacity-30"
        >
          Loading Portfolio
        </p>
        <div class="relative inline-block">
          <span class="font-ubermove-bold text-8xl leading-none md:text-[12vw]"
            >{{ percentage }}%</span
          >
        </div>
      </div>
      <div class="absolute bottom-20 h-[1px] w-40 bg-white/20">
        <div
          class="h-full bg-white transition-all duration-100"
          :style="{ width: percentage + '%' }"
        ></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Reset de scroll lateral para evitar bugs no GSAP */
:global(html, body) {
  margin: 0;
  padding: 0;
  width: 100%;
  overflow-x: hidden !important;
}

@font-face {
  font-family: 'UberMove';
  src: url('/fonts/UberMoveMedium.otf') format('opentype'); /* Ajuste o formato se for .otf */
  font-weight: 400; /* Equivalente ao 'normal' */
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'UberMove';
  src: url('/fonts/UberMoveBold.otf') format('opentype');
  font-weight: 700; /* Equivalente ao 'bold' */
  font-style: normal;
  font-display: swap;
}

/* Classe para o texto Normal/Medium */
.font-ubermove {
  font-family: 'UberMove', sans-serif;
  font-weight: 400;
  -webkit-font-smoothing: antialiased;
}

/* Classe para o texto Bold */
.font-ubermove-bold {
  font-family: 'UberMove', sans-serif;
  font-weight: 700;
  -webkit-font-smoothing: antialiased;
}

.noise-container::before {
  content: '';
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url('https://grainy-gradients.vercel.app/noise.svg');
  opacity: 0.04;
  pointer-events: none;
  z-index: 999;
}

.image-reveal {
  clip-path: polygon(0% 0%, 100% 0%, 100% 0%, 0% 0%);
  will-change: clip-path;
}

.div-horizontal {
  will-change: transform;
}

.secao-horizontal {
  flex-shrink: 0;
  width: 100vw;
  height: 100vh;
}
</style>
