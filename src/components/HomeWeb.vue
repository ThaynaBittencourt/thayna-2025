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
    year: '2026',
    index: '01',
    image:
      'https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1000&auto=format&fit=crop',
  },
  {
    title: 'AUTOMAÇÃO',
    category: 'Workflow / n8n',
    year: '2026',
    index: '02',
    image:
      'https://images.unsplash.com/photo-1518770660439-4636190af475?q=80&w=1000&auto=format&fit=crop',
  },
  {
    title: 'Meus projetos',
    category: 'vamos criar juntos?',
    year: '2025',
    index: '03',
    image:
      'https://images.unsplash.com/photo-1555066931-4365d14bab8c?q=80&w=1000&auto=format&fit=crop',
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

const isMobile = ref(window.innerWidth < 1024);

// dentro do onMounted, adicione:
window.addEventListener('resize', () => {
  isMobile.value = window.innerWidth < 1024;
});

onUnmounted(() => {
  ScrollTrigger.getAll().forEach((t) => t.kill());
});
</script>

<template>
  <div
    class="noise-container min-h-screen bg-[#000000] text-black selection:bg-black selection:text-white"
  >
    <section
      class="relative flex min-h-screen items-center overflow-hidden px-8 lg:px-20"
    >
      <div class="max-w-[1400px]">
        <div
          class="grid w-full max-w-[1600px] grid-cols-1 items-center gap-16 lg:grid-cols-2"
        >
          <div class="z-10 order-2 lg:order-1">
            <div class="flex flex-col justify-center">
              <div class="pb-10">
                <h1
                  class="reveal-header font-ubermove-bold translate-y-[100px] text-amber-600 lg:skew-y-4 skew-y-0 text-[8vw] leading-[0.85] font-bold tracking-tighter uppercase opacity-0 lg:text-[5vw] drop-shadow-xl drop-shadow-white/10"
                >
                  Thaynã <br />
                  <span
                    class="lg:text-[6vw] text-[10vw] font-bold font-ubermove-bold text-gray-200"
                    >Bittencourt</span
                  >
                </h1>
              </div>
              <div>
                <p
                  class="font-ubermove mt-6 max-w-md lg:skew-y-4 skew-y-0 text-xl text-zinc-600"
                >
                  Desenvolvedor Full Stack especializado em ecossistema Laravel,
                  Vue.js e automações com n8n.
                </p>
                <div v-if="isMobile" class="mb-40">
                  <span
                    class="reveal-text inline-block translate-y-full text-xs font-bold tracking-widest text-amber-600 uppercase"
                  >
                    Based in Natal, RN / Full Stack Developer
                  </span>
                </div>
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
                src="/src/assets/img/eu preto e branco.jpeg"
                alt="Thaynã"
                class="hero-img h-full w-full scale-110 object-cover lg:object-[center_38%] object-[center_35%] grayscale"
              />
            </div>
          </div>
        </div>
      </div>
    </section>

    <section
      class="div-horizontal flex h-screen lg:w-[350vw] flex-nowrap overflow-x-hidden bg-white text-white"
    >
      <div
        class="secao-horizontal flex h-full w-screen items-center justify-center bg-black/97 border-r border-white/10"
      >
        <h1
          class="font-ubermove text-[10vw] text-amber-600/50 font-bold uppercase"
        >
          Agilidade
        </h1>
      </div>
      <div
        class="secao-horizontal flex h-full w-screen items-center justify-center border-r bg-amber-600/35 border-white/10"
      >
        <h1 class="font-ubermove text-[10vw] text-amber-600/50 font-bold uppercase">
          Modernidade
        </h1>
      </div>
      <div
        class="secao-horizontal flex h-full w-screen items-center justify-center bg-black/97"
      >
        <h1
          class="font-ubermove text-[10vw] text-amber-600/50 font-bold uppercase"
        >
          Inovação
        </h1>
      </div>
    </section>

    <section id="projetos" class="projects-section">
      <div
        class="mb-20 flex flex-col py-15 items-end justify-between gap-8 md:flex-row"
      >
        <h2
          class="font-ubermove-bold text-5xl text-amber-600 tracking-tighter uppercase"
        >
          Selected <br />
          <span class="text-zinc-600">Works</span>
        </h2>
        <p class="max-w-xs text-sm text-zinc-400 italic">
          Projetos desenvolvidos com foco em performance, escalabilidade e
          experiência do usuário.
        </p>
      </div>

      <div class="projects-grid">
        <div v-for="(project, idx) in projects" :key="idx" class="project-card">
          <div class="project-img-wrap">
            <img
              :src="project.image"
              :alt="project.title"
              class="project-img"
            />
            <!-- <div class="project-overlay">
              <span class="overlay-cta">Ver projeto <ArrowUpRight :size="16" /></span>
            </div> -->
          </div>
          <div class="project-info">
            <div class="project-info-left">
              <span class="project-index">{{ project.index }}</span>
              <div>
                <p class="project-cat">{{ project.category }}</p>
                <h3 class="project-title">{{ project.title }}</h3>
              </div>
            </div>
            <span class="project-year">{{ project.year }}</span>
          </div>
        </div>
      </div>
    </section>

    <!-- <section class="flex h-screen items-center justify-center bg-[#F9F9F7]">
      <h2
        translate="no"
        class="font-ubermove text-[15vw] font-bold tracking-tighter text-black uppercase opacity-5"
      >
        developer
      </h2>
    </section> -->

    <section
      class="flex h-[70vh] flex-col items-center justify-center bg-[#F9F9F7] px-6 text-center"
    >
      <h2
        class="font-ubermove-bold text-[10vw] leading-none tracking-tighter uppercase mb-12"
      >
        Vamos criar <br /><span class="outline-text">algo novo?</span>
      </h2>
      <a
        href="mailto:contato@exemplo.com"
        class="text-2xl font-ubermove-bold underline underline-offset-8 hover:text-emerald-600 transition-colors"
      >
        Get in touch
      </a>
    </section>

    <div
      v-if="isLoading"
      class="loader-container h-full fixed inset-0 z-[9999] flex flex-col items-center justify-center bg-black text-white"
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

.outline-text {
  -webkit-text-stroke: 1.5px black;
  color: transparent;
}

.outline-text-white {
  -webkit-text-stroke: 1px white;
  color: transparent;
}

/* ══════════════════════════════════
   PROJETOS
══════════════════════════════════ */
.projects-section {
  background: var(--bg-2);
  padding: 5rem 1.5rem 6rem;
  border-top: 1px solid var(--border);
}
@media (min-width: 1024px) {
  .projects-section {
    padding: 7rem 5rem 8rem;
  }
}

.projects-header {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  margin-bottom: 4rem;
  padding-bottom: 2.5rem;
  border-bottom: 1px solid var(--border);
}
@media (min-width: 768px) {
  .projects-header {
    flex-direction: row;
    align-items: flex-end;
    justify-content: space-between;
  }
}

.section-label {
  font-size: 0.7rem;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--accent);
  display: block;
  margin-bottom: 1rem;
}
.section-title {
  font-size: clamp(2.5rem, 6vw, 4rem);
  font-weight: 700;
  letter-spacing: -0.04em;
  line-height: 1;
  text-transform: uppercase;
  margin: 0;
}
.title-muted {
  color: #2a2a2a;
}
.projects-header-desc {
  font-size: 0.85rem;
  line-height: 1.7;
  color: var(--muted);
  text-align: left;
}
@media (min-width: 768px) {
  .projects-header-desc {
    text-align: right;
  }
}

.projects-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 3rem;
}
@media (min-width: 1024px) {
  .projects-grid {
    grid-template-columns: repeat(3, 1fr);
    gap: 2rem;
  }
}

.project-card {
  cursor: pointer;
}
.project-img-wrap {
  position: relative;
  aspect-ratio: 3/4;
  overflow: hidden;
  border-radius: 8px;
  background: var(--surface);
  margin-bottom: 1.25rem;
}
.project-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: grayscale(60%);
  transition:
    filter 0.6s ease,
    transform 0.6s ease;
}
.project-card:hover .project-img {
  filter: grayscale(0%);
  transform: scale(1.04);
}
.project-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: flex-end;
  padding: 1.5rem;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.7) 0%, transparent 50%);
  opacity: 0;
  transition: opacity 0.4s;
}
.project-card:hover .project-overlay {
  opacity: 1;
}
.overlay-cta {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: #fff;
}

.project-info {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  padding-top: 1rem;
  border-top: 1px solid var(--border);
}
.project-info-left {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
}
.project-index {
  font-size: 0.65rem;
  color: var(--muted);
  letter-spacing: 0.1em;
  padding-top: 0.2rem;
}
.project-cat {
  font-size: 0.65rem;
  font-weight: 400;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--accent);
  margin-bottom: 0.3rem;
}
.project-title {
  font-size: 1.1rem;
  font-weight: 700;
  letter-spacing: -0.02em;
  text-transform: uppercase;
  color: oklch(66.6% 0.179 58.318);
}
.project-year {
  font-size: 0.65rem;
  color: var(--muted);
  letter-spacing: 0.08em;
  font-family: 'Courier New', monospace;
}

.hero-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center 35%;
  filter: grayscale(30%) contrast(1.05);
  transform: scale(1.05);
}
</style>
