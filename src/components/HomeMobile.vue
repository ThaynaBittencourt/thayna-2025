<!-- eslint-disable vue/block-lang -->
<script setup>
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { ArrowUpRight, Github, Linkedin, Mail } from 'lucide-vue-next';
import { onMounted, onUnmounted, ref, nextTick } from 'vue';

gsap.registerPlugin(ScrollTrigger);

const percentage = ref(0);
const isLoading = ref(true);

const projects = [
  {
    title: 'INTEGRAÇÃO COM IA',
    category: 'Inteligência Artificial',
    year: '2026',
    image:
      'https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1000&auto=format&fit=crop',
  },
  {
    title: 'TESTE',
    category: 'Workflow',
    year: '2026',
    image:
      'https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1000&auto=format&fit=crop',
  },
  {
    title: 'Meus projetos',
    category: 'vamos criar juntos?',
    year: '2025',
    image:
      'https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1000&auto=format&fit=crop',
  },
];

onMounted(() => {
  const tlLoader = gsap.timeline({ defaults: { ease: 'power2.inOut' } });
  const counter = { value: 0 };

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
      isLoading.value = false;
      ScrollTrigger.getAll().forEach((t) => t.kill());
      nextTick().then(() => iniciarAnimacoesPrincipais());
    });
});

function iniciarAnimacoesPrincipais() {
  gsap.to('.reveal-header', {
    opacity: 1,
    y: 0,
    stagger: 0.1,
    skewY: 0,
    duration: 1.5,
    ease: 'power4.out',
  });

  gsap.to('.image-reveal', {
    clipPath: 'polygon(0% 0%, 100% 0%, 100% 100%, 0% 100%)',
    stagger: 0.2,
    duration: 1.5,
    ease: 'expo.out',
  });

  const slider = document.querySelector('.div-horizontal');
  const innerSlider = document.querySelector('.inner-slider');
  const sections = gsap.utils.toArray('.secao-horizontal');

  if (slider && innerSlider && sections.length > 0) {
    ScrollTrigger.refresh();

    const sliderTl = gsap.timeline({
      scrollTrigger: {
        trigger: slider,
        pin: true,
        pinSpacing: true,
        scrub: 1,
        snap: 1 / (sections.length - 1),
        start: 'top top',
        end: () => `+=${innerSlider.scrollWidth - window.innerWidth}`,
        invalidateOnRefresh: true,
        anticipatePin: 1,
      },
    });

    sliderTl.to(sections, {
      xPercent: -100 * (sections.length - 1),
      ease: 'none',
    });
  }
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
    <!-- HERO -->
    <section
      class="relative flex min-h-screen flex-col items-center justify-center px-6 pt-20 pb-12"
    >
      <!-- Nome (topo) -->
      <div class="w-full">
        <h1
          class="reveal-header font-ubermove-bold translate-y-[100px] text-[8vw] leading-[0.85] font-bold tracking-tighter uppercase opacity-0"
        >
          Thaynã <br />
          <span class="text-[10vw] font-bold font-ubermove-bold text-gray-400"
            >Bittencourt</span
          >
        </h1>
      </div>

      <!-- Foto (meio) -->
      <div class="reveal-header my-8 w-full translate-y-[60px] opacity-0">
        <div
          class="hero-img-wrapper aspect-[4/4] w-full overflow-hidden rounded-2xl bg-zinc-200"
        >
          <img
            src="/src/assets/img/eu preto e branco.jpeg"
            alt="Thaynã"
            class="hero-img h-full w-full scale-110 object-cover object-[center_35%] grayscale"
          />
        </div>
        <div class="pb-5 pt-3 justify-center flex items-center overflow-hidden">
          <span
            class="reveal-header inline-block translate-y-[60px] text-[10px] font-bold tracking-widest font-ubermove text-emerald-600 uppercase opacity-0"
          >
            Based in Natal, RN / Full Stack Developer
          </span>
        </div>
      </div>

      <!-- Descrição (baixo) -->
      <div class="w-full py-10">
        <p
          class="reveal-header font-ubermove translate-y-[60px] text-base text-zinc-600 opacity-0"
        >
          Desenvolvedor Full Stack especializado em ecossistema Laravel, Vue.js
          e automações com n8n.
        </p>

        <div class="reveal-header pt-6 flex translate-y-[60px] gap-5 opacity-0">
          <a href="https://github.com/ThaynaBittencourt/" target="_blank">
            <Github
              class="h-5 w-5 cursor-pointer transition-colors hover:text-emerald-600"
            />
          </a>
          <a
            href="https://www.linkedin.com/in/thayn%C3%A3-bittencourt-470571274/"
            target="_blank"
          >
            <Linkedin
              class="h-5 w-5 cursor-pointer transition-colors hover:text-emerald-600"
            />
          </a>
          <a href="mailto:professornegreco@gmail.com">
            <Mail
              class="h-5 w-5 cursor-pointer transition-colors hover:text-emerald-600"
            />
          </a>
        </div>
      </div>
    </section>

    <!-- SCROLL HORIZONTAL -->
    <div class="div-horizontal h-screen overflow-hidden w-screen">
      <div
        class="flex h-full w-[300vw] flex-nowrap bg-black text-white inner-slider"
      >
        <div
          class="secao-horizontal flex h-full w-screen items-center justify-center border-r border-white/10"
        >
          <h1 class="font-ubermove text-[10vw] font-bold uppercase">
            Agilidade
          </h1>
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
          <h1 class="font-ubermove text-[10vw] font-bold uppercase">
            Inovação
          </h1>
        </div>
      </div>
    </div>

    <!-- PROJETOS -->
    <section class="bg-black px-6 py-16 text-white">
      <div class="mb-10 flex flex-col gap-4">
        <h2 class="font-ubermove-bold text-4xl tracking-tighter uppercase">
          Selected <br />
          <span class="text-zinc-600">Works</span>
        </h2>
        <p class="max-w-xs text-sm text-zinc-400 italic">
          Projetos desenvolvidos com foco em performance, escalabilidade e
          experiência do usuário.
        </p>
      </div>

      <div class="flex flex-col gap-12">
        <div
          v-for="(project, idx) in projects"
          :key="idx"
          class="group cursor-pointer"
        >
          <div
            class="image-reveal relative aspect-[3/4] overflow-hidden rounded-xl bg-zinc-900"
          >
            <img
              :src="project.image"
              class="h-full w-full object-cover opacity-50 grayscale transition-all duration-700 group-active:scale-105 group-active:opacity-100 group-active:grayscale-0"
            />
            <div
              class="absolute top-4 right-4 rounded-full border border-white/20 bg-black/20 p-3 backdrop-blur-md"
            >
              <ArrowUpRight class="h-4 w-4" />
            </div>
          </div>
          <div class="mt-5 flex items-start justify-between">
            <div>
              <p
                class="mb-1 text-[10px] font-bold tracking-widest text-emerald-500 uppercase"
              >
                {{ project.category }}
              </p>
              <h3 class="font-ubermove-bold text-2xl tracking-tighter">
                {{ project.title }}
              </h3>
            </div>
            <span class="font-mono text-xs text-zinc-600">{{
              project.year
            }}</span>
          </div>
        </div>
      </div>
    </section>

    <!-- WATERMARK -->
    <section class="flex h-screen items-center justify-center bg-[#F9F9F7]">
      <h2
        translate="no"
        class="font-ubermove text-[15vw] font-bold tracking-tighter text-black uppercase opacity-5"
      >
        developer
      </h2>
    </section>

    <!-- LOADER -->
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
        <span class="font-ubermove-bold text-[22vw] leading-none">
          {{ percentage }}%
        </span>
      </div>
      <div class="absolute bottom-12 h-[1px] w-32 bg-white/20">
        <div
          class="h-full bg-white transition-all duration-100"
          :style="{ width: percentage + '%' }"
        ></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
:global(html) {
  overflow-x: hidden;
}

:global(body) {
  margin: 0;
  padding: 0;
  max-width: 100vw;
}

@font-face {
  font-family: 'UberMove';
  src: url('/fonts/UberMoveMedium.otf') format('opentype');
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'UberMove';
  src: url('/fonts/UberMoveBold.otf') format('opentype');
  font-weight: 700;
  font-style: normal;
  font-display: swap;
}

.font-ubermove {
  font-family: 'UberMove', sans-serif;
  font-weight: 400;
  -webkit-font-smoothing: antialiased;
}

.font-ubermove-bold {
  font-family: 'UberMove', sans-serif;
  font-weight: 700;
  -webkit-font-smoothing: antialiased;
}

.noise-container::before {
  content: '';
  position: relative;
  isolation: isolate;
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
