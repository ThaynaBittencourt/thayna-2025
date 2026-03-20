<!-- eslint-disable vue/block-lang -->
<script setup>
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import {
  Github,
  Linkedin,
  Mail,
  ArrowUpRight,
  ExternalLink,
  Instagram,
} from "lucide-vue-next";
import { onMounted, onUnmounted, ref, nextTick } from "vue";

gsap.registerPlugin(ScrollTrigger);

// ─── Estado Global ───────────────────────────────────────────────
const percentage = ref(0);
const isLoading = ref(true);
const cursorX = ref(0);
const cursorY = ref(0);
const cursorVisible = ref(false);
const cursorLabel = ref("");
const isMobile = ref(false);

// ─── Projetos ────────────────────────────────────────────────────
const projects = [
  {
    title: "JáBusca",
    category: "Marketplace",
    year: "2026",
    index: "01",
    image: new URL("../assets/img/JaBusca.png", import.meta.url).href,
  },
  {
    title: "Detran-PB",
    category: "Novo sistema detran da Paraíba",
    year: "2026",
    index: "02",
    image: new URL("../assets/img/detranPb.png", import.meta.url).href,
  },
  {
    title: "SIGA",
    category: "Tela de Serviços do siga/IDEMA",
    year: "2026",
    index: "02",
    image: new URL("../assets/img/idema.png", import.meta.url).href,
  },
  {
    title: "Docbuilder",
    category: "Sistema gerador de documentos com iA",
    year: "2025",
    index: "03",
    image: new URL("../assets/img/Docbuilder.png", import.meta.url).href,
  },
  {
    title: "POSTO+",
    category: "Sistema Marketplace para Ubers & etc.",
    year: "2025",
    index: "03",
    image: new URL("../assets/img/posto+.png", import.meta.url).href,
  },
];

// ─── Skills ──────────────────────────────────────────────────────
const skills = [
  "Html",
  "Css",
  "JS",
  "Vue.js",
  "n8n",
  "MySQL",
  "HeidiSql",
  "Tailwind",
  "GSAP",
  "Laravel",
  "PHP",
];

// ─── Image Trailer ───────────────────────────────────────────────
const trailerRef = ref(null); // hero section (sem pássaros)
const trailerRef2 = ref(null); // cta section  (com pássaros)

const birdSrcs = [
  "https://ahmedabdalalim.pages.dev/images/pirds/p5.gif",
  "https://ahmedabdalalim.pages.dev/images/pirds/p6.gif",
  "https://ahmedabdalalim.pages.dev/images/pirds/p7.gif",
  "https://ahmedabdalalim.pages.dev/images/pirds/p8.gif",
  "https://ahmedabdalalim.pages.dev/images/pirds/p9.gif",
  "https://ahmedabdalalim.pages.dev/images/pirds/p10.gif",
];

const birds = ref(
  birdSrcs.map(() => ({
    x: 0,
    y: 0,
    vx: 0,
    vy: 0,
    flipped: false,
    visible: false,
  })),
);

// Tesoura
const trailerCursorX = ref(0);
const trailerCursorY = ref(0);
const trailerCursorAngle = ref(150);
const trailerCursorActive = ref(false);
const trailerCursorVisible = ref(false);
let prevCursorX = 0,
  prevCursorY = 0,
  cursorMoveTimer = null;
let trailerRafId = null;

function onTrailerMouseMove(e) {
  const el = trailerRef2.value;
  if (!el) return;
  const rect = el.getBoundingClientRect();
  const x = e.clientX - rect.left;
  const y = e.clientY - rect.top;
  const dx = x - prevCursorX,
    dy = y - prevCursorY;
  if (Math.abs(dx) > 1 || Math.abs(dy) > 1) {
    trailerCursorAngle.value = Math.atan2(dy, dx) * (180 / Math.PI) + 90;
    trailerCursorActive.value = true;
    clearTimeout(cursorMoveTimer);
    cursorMoveTimer = setTimeout(() => {
      trailerCursorActive.value = false;
    }, 150);
  }
  prevCursorX = x;
  prevCursorY = y;
  trailerCursorX.value = x;
  trailerCursorY.value = y;
  trailerCursorVisible.value = true;
}

function animateBirds() {
  const el = trailerRef2.value;
  if (!el) {
    trailerRafId = requestAnimationFrame(animateBirds);
    return;
  }
  const W = el.offsetWidth;
  const H = el.offsetHeight;
  const S = Math.min(W * 0.07, 116);

  birds.value.forEach((b) => {
    b.x += b.vx;
    b.y += b.vy;
    if (b.x <= 0) {
      b.x = 0;
      b.vx = Math.abs(b.vx);
    }
    if (b.x >= W - S) {
      b.x = W - S;
      b.vx = -Math.abs(b.vx);
    }
    if (b.y <= 0) {
      b.y = 0;
      b.vy = Math.abs(b.vy);
    }
    if (b.y >= H - S) {
      b.y = H - S;
      b.vy = -Math.abs(b.vy);
    }
    b.vx += (Math.random() - 0.5) * 0.08;
    b.vy += (Math.random() - 0.5) * 0.08;
    const sp = Math.sqrt(b.vx * b.vx + b.vy * b.vy);
    const mn = 0.8,
      mx = 2.4;
    if (sp > mx) {
      b.vx = (b.vx / sp) * mx;
      b.vy = (b.vy / sp) * mx;
    }
    if (sp < mn) {
      b.vx = (b.vx / sp) * mn;
      b.vy = (b.vy / sp) * mn;
    }
    b.flipped = b.vx < 0;
    b.visible = true;
  });
  trailerRafId = requestAnimationFrame(animateBirds);
}

function startTrailer() {
  if (trailerRafId) return;
  const el = trailerRef2.value;
  if (!el) {
    setTimeout(startTrailer, 100);
    return;
  }
  const W = el.offsetWidth,
    H = el.offsetHeight;
  birds.value.forEach((b) => {
    b.x = Math.random() * (W * 0.8) + W * 0.1;
    b.y = Math.random() * (H * 0.8) + H * 0.1;
    const angle = Math.random() * Math.PI * 2;
    const speed = 1.0 + Math.random() * 1.4;
    b.vx = Math.cos(angle) * speed;
    b.vy = Math.sin(angle) * speed;
    b.flipped = b.vx < 0;
    b.visible = false;
  });
  animateBirds();
}

function stopTrailer() {
  if (trailerRafId) {
    cancelAnimationFrame(trailerRafId);
    trailerRafId = null;
  }
}

// ─── Cursor Global ───────────────────────────────────────────────
function moveCursor(e) {
  cursorX.value = e.clientX;
  cursorY.value = e.clientY;
  cursorVisible.value = true;
}
function showCursorLabel(label) {
  cursorLabel.value = label;
}
function hideCursorLabel() {
  cursorLabel.value = "";
}

// ─── Lifecycle ───────────────────────────────────────────────────
onMounted(() => {
  isMobile.value = window.innerWidth < 1024;
  window.addEventListener("resize", () => {
    isMobile.value = window.innerWidth < 1024;
  });
  window.addEventListener("mousemove", moveCursor);

  const counter = { value: 0 };
  const tlLoader = gsap.timeline({ defaults: { ease: "power2.inOut" } });

  tlLoader
    .to(counter, {
      value: 100,
      duration: 2.2,
      onUpdate: () => {
        percentage.value = Math.floor(counter.value);
      },
    })
    .to(".loader-bar-fill", { width: "100%", duration: 2.2 }, 0)
    .to(".loader-container", {
      yPercent: -100,
      duration: 1,
      ease: "expo.inOut",
      delay: 0.3,
    })
    .add(() => {
      isLoading.value = false;
      ScrollTrigger.getAll().forEach((t) => t.kill());
      nextTick().then(() => iniciarAnimacoesPrincipais());
    });
});

function iniciarAnimacoesPrincipais() {
  gsap.to(".site-nav", {
    opacity: 1,
    y: 0,
    duration: 1,
    ease: "power3.out",
    delay: 0.2,
  });

  gsap.utils.toArray(".hero-line").forEach((el, i) => {
    gsap.to(el, {
      opacity: 1,
      y: 0,
      duration: 1.1,
      ease: "expo.out",
      delay: 0.1 + i * 0.12,
    });
  });

  gsap.to(".hero-img-wrap", {
    opacity: 1,
    scale: 1,
    duration: 1.4,
    ease: "expo.out",
    delay: 0.3,
  });

  const slider = document.querySelector(".div-horizontal");
  const sections = gsap.utils.toArray(".secao-horizontal");
  if (slider && sections.length) {
    gsap
      .timeline({
        scrollTrigger: {
          trigger: slider,
          pin: true,
          scrub: 1,
          snap: 1 / (sections.length - 1),
          start: "top top",
          end: () => "+=" + (slider.scrollWidth - window.innerWidth),
          invalidateOnRefresh: true,
        },
      })
      .to(sections, { xPercent: -100 * (sections.length - 1), ease: "none" });
  }

  gsap.utils.toArray(".project-card").forEach((card, i) => {
    gsap.from(card, {
      opacity: 0,
      y: 60,
      duration: 0.8,
      ease: "power3.out",
      scrollTrigger: { trigger: card, start: "top 85%" },
      delay: i * 0.1,
    });
  });

  gsap.from(".cta-title", {
    opacity: 0,
    y: 50,
    duration: 1,
    ease: "power3.out",
    scrollTrigger: { trigger: ".cta-section-wrap", start: "top 70%" },
  });

  startTrailer();
  ScrollTrigger.refresh();
}

onUnmounted(() => {
  ScrollTrigger.getAll().forEach((t) => t.kill());
  window.removeEventListener("mousemove", moveCursor);
  stopTrailer();
});
</script>

<template>
  <div class="portfolio-root">
    <!-- Cursor -->
    <div
      v-if="!isMobile"
      class="custom-cursor"
      :class="{ labeled: !!cursorLabel }"
      :style="{ left: cursorX + 'px', top: cursorY + 'px' }"
    >
      <span v-if="cursorLabel" class="cursor-label">{{ cursorLabel }}</span>
    </div>

    <!-- Loader -->
    <div v-if="isLoading" class="loader-container">
      <div class="loader-inner">
        <div class="loader-name">Thaynã Bittencourt</div>
        <div class="loader-percent">
          {{ percentage }}<span class="loader-symbol">%</span>
        </div>
        <div class="loader-bar"><div class="loader-bar-fill"></div></div>
        <div class="loader-sub">Loading Portfolio</div>
      </div>
    </div>

    <!-- Navbar -->
    <nav class="site-nav">
      <div class="nav-logo">TB<span class="nav-dot">.</span></div>
      <div class="nav-links">
        <a href="#hero" class="nav-link">Início</a>
        <a href="#projetos" class="nav-link">Projetos</a>
        <a href="#contato" class="nav-link">Contato</a>
      </div>
      <a
        href="https://wa.me/5584996839198?text=Olá%20Thaynã%2C%20vim%20pelo%20seu%20portfolio!"
        target="_blank"
        class="nav-cta"
      >
        Whats <ArrowUpRight :size="13" />
      </a>
    </nav>

    <!-- ══════════════════════════════
         HERO  (fundo âmbar)
    ══════════════════════════════ -->
    <section class="hero-trailer" id="hero" ref="trailerRef">
      <div class="it-grid"></div>

      <!-- Plantas decorativas -->
      <img
        src="../assets/img/planta.png"
        alt="Planta bottom L"
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -bottom-8 -left-8 rotate-45"
        style="width: clamp(80px, 12vw, 230px)"
      />
      <img
        src="../assets/img/planta2.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -top-8 -right-8 -rotate-[130deg]"
        style="width: clamp(80px, 12vw, 230px)"
      />
      <img
        src="../assets/img/planta.png"
        alt="Planta top L"
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl bottom-[83%] -left-6 rotate-[130deg]"
        style="width: clamp(80px, 12vw, 230px)"
      />
      <img
        src="../assets/img/planta2.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl top-[35%] -right-8 -rotate-[35deg]"
        style="width: clamp(80px, 12vw, 230px)"
      />
      <img
        src="../assets/img/planta3.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -bottom-2 right-[20%]"
        style="width: clamp(40px, 5vw, 110px)"
      />
      <img
        src="../assets/img/planta3.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -top-2 left-[20%] rotate-180"
        style="width: clamp(40px, 5vw, 110px)"
      />

      <div class="hero-grid">
        <!-- Texto -->
        <div class="hero-text">
          <h1 class="hero-heading">
            <span class="hero-line hero-name-1 font-ubermove-bold text-gray-800"
            >Thaynã</span
            >
            <span class="hero-line hero-name-2 font-ubermove-bold text-gray-900"
            >Bittencourt</span
            >
          </h1>
          <div class="hero-tag hero-line">
            <span class="dot"></span> Residente em Natal, RN · Desenvolvedor Full Stack
          </div>
          <p class="hero-desc hero-line">
            Desenvolvedor Full Stack especializado em
            <em>Laravel</em>, <em>Vue.js</em> e automações com <em>n8n</em>.
          </p>
          <div class="hero-actions hero-line">
            <a href="#projetos" class="btn-primary">Ver projetos</a>
            <div class="hero-socials">
              <a
                href="#"
                class="social-icon"
                aria-label="GitHub"
                @mouseenter="showCursorLabel('GitHub')"
                @mouseleave="hideCursorLabel()"
              >
                <Github :size="17" />
              </a>
              <a
                href="#"
                class="social-icon"
                aria-label="LinkedIn"
                @mouseenter="showCursorLabel('LinkedIn')"
                @mouseleave="hideCursorLabel()"
              >
                <Linkedin :size="17" />
              </a>
              <a
                href="mailto:contato@exemplo.com"
                class="social-icon"
                aria-label="Email"
                @mouseenter="showCursorLabel('Email')"
                @mouseleave="hideCursorLabel()"
              >
                <Mail :size="17" />
              </a>
            </div>
          </div>
        </div>

        <!-- Foto -->
        <div class="hero-img-wrap">
          <div class="hero-img-inner">
            <img
              src="/src/assets/img/eu style.jpeg"
              alt="Thaynã Bittencourt"
              class="hero-photo"
            />
            <div class="hero-img-badge">
              <span class="badge-dot"></span><span>Disponível</span>
            </div>
          </div>
          <div class="hero-img-bg"></div>
        </div>
      </div>

      <div class="scroll-hint">
        <span>Scroll</span>
        <div class="scroll-line"><div class="scroll-line-fill"></div></div>
      </div>
    </section>

    <!-- ══════════════════════════════
         MARQUEE
    ══════════════════════════════ -->
    <div class="marquee-section">
      <div class="skills-track-wrap">
        <div class="skills-track">
          <template v-for="n in 2" :key="n">
            <span v-for="skill in skills" :key="skill + n" class="skill-chip">
              {{ skill }} <span class="skill-sep">·</span>
            </span>
          </template>
        </div>
      </div>
    </div>

    <!-- ══════════════════════════════
         SCROLL HORIZONTAL
    ══════════════════════════════ -->
    <section class="div-horizontal">
      <div class="secao-horizontal s1">
        <div class="word-wrap">
          <span class="word-stroke">Agilidade</span>
          <span class="word-solid">Agilidade</span>
        </div>
      </div>
      <div class="secao-horizontal s2">
        <div class="word-wrap">
          <span class="word-stroke">Modernidade</span>
          <span class="word-solid">Modernidade</span>
        </div>
      </div>
      <div class="secao-horizontal s3">
        <div class="word-wrap">
          <span class="word-stroke">Inovação</span>
          <span class="word-solid">Inovação</span>
        </div>
      </div>
    </section>

    <!-- ══════════════════════════════
         PROJETOS
    ══════════════════════════════ -->
    <section id="projetos" class="projects-section">
      <div class="projects-header">
        <div>
          <span class="section-label"></span>
          <h2 class="section-title">
            Projetos<br /><span class="title-muted">Desenvolvido</span>
          </h2>
        </div>
        <p class="projects-header-desc">
          Projetos com foco em performance,<br />escalabilidade e experiência do
          usuário.
        </p>
      </div>

      <div class="projects-grid">
        <div
          v-for="(project, idx) in projects"
          :key="idx"
          class="project-card"
          @mouseenter="showCursorLabel('works')"
          @mouseleave="hideCursorLabel()"
        >
          <div class="project-img-wrap">
            <img
              :src="project.image"
              :alt="project.title"
              class="project-img"
            />
            <div class="project-overlay">
              <!-- <span class="overlay-cta"
              >Ver projeto <ExternalLink :size="13"
              /></span> -->
            </div>
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

    <!-- ══════════════════════════════
         CTA + PÁSSAROS  (fundo âmbar)
    ══════════════════════════════ -->
    <section id="contato" class="cta-section-wrap" ref="trailerRef2">
      <div class="it-grid"></div>

      <!-- Plantas -->
      <img
        src="../assets/img/planta.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -bottom-6 -left-8 rotate-45"
        style="width: clamp(80px, 12vw, 230px)"
      />
      <img
        src="../assets/img/planta.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -top-7 -left-8 rotate-140"
        style="width: clamp(80px, 12vw, 230px)"
      />
      <img
        src="../assets/img/planta2.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -top-8 -right-8 -rotate-[130deg]"
        style="width: clamp(80px, 12vw, 230px)"
      />
      <img
        src="../assets/img/planta3.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -bottom-2 right-[20%]"
        style="width: clamp(40px, 5vw, 110px)"
      />
      <img
        src="../assets/img/planta3.png"
        alt=""
        class="absolute pointer-events-none object-contain z-[2] drop-shadow-xl -top-2 left-[20%] rotate-180"
        style="width: clamp(40px, 5vw, 110px)"
      />

      <!-- Pássaros autônomos -->
      <img
        v-for="(bird, i) in birds"
        :key="i"
        :src="birdSrcs[i]"
        class="it-bird"
        :style="{
          transform: `translate3d(${bird.x}px,${bird.y}px,0)${bird.flipped ? ' rotateY(180deg)' : ''}`,
          zIndex: 6 + (birds.length - i),
          opacity: bird.visible ? 1 : 0,
        }"
        alt=""
      />

      <!-- Tesoura cursor -->
      <!-- <img
        src="https://ahmedabdalalim.pages.dev/images/footer/cutA.gif"
        class="it-cursor-moving"
        :style="{
          left: trailerCursorX + 'px',
          top: trailerCursorY + 'px',
          display: trailerCursorActive ? 'block' : 'none',
          transform: `translate(-50%,-50%) rotate(${trailerCursorAngle}deg)`,
        }"
        alt=""
      />
      <img
        src="https://ahmedabdalalim.pages.dev/images/footer/cut.webp"
        class="it-cursor-idle"
        :style="{
          left: trailerCursorX + 'px',
          top: trailerCursorY + 'px',
          display:
            !trailerCursorActive && trailerCursorVisible ? 'block' : 'none',
          transform: `translate(-50%,-50%) rotate(${trailerCursorAngle}deg)`,
        }"
        alt=""
      /> -->

      <!-- Conteúdo CTA -->
      <div class="cta-inner">
        <span class="section-label cta-label"></span>
        <h2 class="cta-title">
          Vamos criar<br /><span class="cta-outline">algo novo?</span>
        </h2>
        <a
          href="mailto:thaynabittencourt.ads@gmail.com?subject=Contato via Portfolio"
          class="cta-email"
        >
          thaynabittencourt.ads@gmail.com
          <ArrowUpRight :size="20" class="cta-arrow" />
        </a>
        <div class="cta-socials">
          <a href="#" class="cta-social-link"><Github :size="16" /> GitHub</a>
          <a href="https://www.linkedin.com/in/thayn%C3%A3-bittencourt-470571274/" class="cta-social-link"
            ><Linkedin :size="16" /> LinkedIn</a
          >
          <a href="https://www.instagram.com/negrescooficial/" class="cta-social-link"
            ><Instagram :size="16" /> Instagram</a
          >
        </div>
      </div>
      <div class="cta-bg-text" aria-hidden="true">TB</div>
    </section>

    <!-- Footer -->
    <footer class="site-footer">
      <span>© 2026 Thaynã Bittencourt</span>
      <!-- <span>Full Stack Developer · Natal, RN</span> -->
    </footer>
  </div>
</template>

<style scoped>
/* ══════════════════════════════════════
   RESET / TOKENS
══════════════════════════════════════ */
:global(*, *::before, *::after) {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
:global(html) {
  scroll-behavior: smooth;
  overflow-x: hidden;
}
:global(body) {
  overflow-x: hidden;
  background: #080808;
}

@font-face {
  font-family: "UberMove";
  src: url("/fonts/UberMoveMedium.otf") format("opentype");
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}
@font-face {
  font-family: "UberMove";
  src: url("/fonts/UberMoveBold.otf") format("opentype");
  font-weight: 700;
  font-style: normal;
  font-display: swap;
}

.font-ubermove {
  font-family: "UberMove", sans-serif;
  font-weight: 400;
}
.font-ubermove-bold {
  font-family: "UberMove", sans-serif;
  font-weight: 700;
}

.portfolio-root {
  --c-bg: #080808;
  --c-surface: #111111;
  --c-border: rgba(255, 255, 255, 0.07);
  --c-accent: #d4a847;
  --c-accent-dim: rgba(212, 168, 71, 0.12);
  --c-text: #e8e8e8;
  --c-muted: #5a5a5a;
  --c-white: #f2f2f2;
  --c-amber: #f5a623;
  --font: "UberMove", "Helvetica Neue", Arial, sans-serif;

  font-family: var(--font);
  background: var(--c-bg);
  color: var(--c-text);
  min-height: 100vh;
  overflow-x: hidden;
  cursor: none;
}

/* ══════════════════════════════════════
   CURSOR
══════════════════════════════════════ */
.custom-cursor {
  position: fixed;
  width: 10px;
  height: 10px;
  background: var(--c-accent);
  border-radius: 50%;
  pointer-events: none;
  z-index: 99999;
  transform: translate(-50%, -50%);
  transition:
    width 0.2s,
    height 0.2s;
  mix-blend-mode: difference;
}
.custom-cursor.labeled {
  width: 64px;
  height: 64px;
  mix-blend-mode: normal;
  display: flex;
  align-items: center;
  justify-content: center;
}
.cursor-label {
  font-size: 0.6rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: #000;
}

/* ══════════════════════════════════════
   LOADER
══════════════════════════════════════ */
.loader-container {
  position: fixed;
  inset: 0;
  z-index: 9999;
  background: #080808;
  display: flex;
  align-items: center;
  justify-content: center;
}
.loader-inner {
  text-align: center;
}
.loader-name {
  font-size: 0.65rem;
  letter-spacing: 0.3em;
  text-transform: uppercase;
  color: var(--c-muted);
  margin-bottom: 2rem;
}
.loader-percent {
  font-family: var(--font);
  font-weight: 700;
  font-size: clamp(5rem, 15vw, 11rem);
  line-height: 1;
  color: var(--c-white);
  letter-spacing: -0.04em;
}
.loader-symbol {
  font-size: 0.5em;
  color: var(--c-accent);
  vertical-align: super;
}
.loader-bar {
  width: 160px;
  height: 1px;
  background: rgba(255, 255, 255, 0.1);
  margin: 2rem auto 1.5rem;
  overflow: hidden;
}
.loader-bar-fill {
  height: 100%;
  width: 0%;
  background: var(--c-accent);
}
.loader-sub {
  font-size: 0.6rem;
  letter-spacing: 0.3em;
  text-transform: uppercase;
  color: var(--c-muted);
}

/* ══════════════════════════════════════
   NAVBAR
══════════════════════════════════════ */
.site-nav {
  background-color: #000000;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 3.5rem;
  opacity: 0;
  transform: translateY(-20px);
  transition:
    border-color 0.3s,
    backdrop-filter 0.3s;
}
.nav-logo {
  font-weight: 700;
  font-size: 1.1rem;
  letter-spacing: -0.02em;
  color: var(--c-white);
}
.nav-dot {
  color: var(--c-accent);
}
.nav-links {
  display: flex;
  gap: 2.5rem;
}
.nav-link {
  font-size: 0.72rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--c-muted);
  text-decoration: none;
  transition: color 0.2s;
}
.nav-link:hover {
  color: var(--c-white);
}
.nav-cta {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  text-decoration: none;
  color: #000;
  background: var(--c-accent);
  padding: 0.6rem 1.2rem;
  border-radius: 2px;
  transition: opacity 0.2s;
}
.nav-cta:hover {
  opacity: 0.85;
}
@media (max-width: 768px) {
  .nav-links {
    display: none;
  }
  .site-nav {
    padding: 1.2rem 1.5rem;
  }
}

/* ══════════════════════════════════════
   SHARED — GRID + FOLHAS + PÁSSAROS
══════════════════════════════════════ */
.it-grid {
  position: absolute;
  inset: 0;
  background-image:
    linear-gradient(rgba(0, 0, 0, 0.12) 1px, transparent 1px),
    linear-gradient(90deg, rgba(0, 0, 0, 0.12) 1px, transparent 1px);
  background-size: 40px 40px;
  opacity: 0.85;
  pointer-events: none;
  z-index: 0;
}

/* .it-leaf {
  position: absolute;
  pointer-events: none;
  object-fit: contain;
  filter: drop-shadow(0 8px 20px rgba(0, 0, 0, 0.22));
  z-index: 2;
}
.it-leaf-bl {
  width: clamp(130px, 17vw, 230px);
  bottom: -2rem;
  left: -2rem;
  transform: rotate(45deg);
}
.it-leaf-tr {
  width: clamp(130px, 17vw, 230px);
  top: -2rem;
  right: -2rem;
  transform: rotate(-130deg);
}
.it-leaf-tr2 {
  width: clamp(130px, 17vw, 230px);
  bottom: 35%;
  left: -1rem;
  transform: rotate(135deg);
}
.it-leaf-br {
  width: clamp(130px, 17vw, 230px);
  top: 35%;
  right: -2rem;
  transform: rotate(-35deg);
}
.it-leaf-bm {
  width: clamp(60px, 7vw, 110px);
  bottom: -0.5rem;
  right: 20%;
  transform: rotate(0deg);
}
.it-leaf-tm {
  width: clamp(60px, 7vw, 110px);
  top: -0.5rem;
  left: 20%;
  transform: rotate(-180deg);
} */

.it-bird {
  position: absolute;
  top: 0;
  left: 0;
  width: clamp(76px, 6.5vw, 116px);
  height: clamp(76px, 6.5vw, 116px);
  object-fit: contain;
  pointer-events: none;
  will-change: transform;
  filter: drop-shadow(0 4px 10px rgba(0, 0, 0, 0.28));
  transform-origin: center;
  transition: opacity 0.3s;
  margin-left: calc(clamp(76px, 6.5vw, 116px) / -2);
  margin-top: calc(clamp(76px, 6.5vw, 116px) / -2);
}

.it-cursor-moving,
.it-cursor-idle {
  position: absolute;
  width: 68px;
  height: 68px;
  pointer-events: none;
  z-index: 20;
  object-fit: contain;
}

/* ══════════════════════════════════════
   HERO TRAILER
══════════════════════════════════════ */
.hero-trailer {
  position: relative;
  width: 100%;
  min-height: 100svh;
  background: var(--c-amber);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  cursor: none;
  padding: 6rem 2rem 4rem;
  user-select: none;
}
@media (min-width: 1024px) {
  .hero-trailer {
    padding: 7rem 5rem 4rem;
  }
}

.hero-grid {
  position: relative;
  z-index: 5;
  display: grid;
  grid-template-columns: 1fr;
  gap: 3rem;
  align-items: center;
  max-width: 1400px;
  width: 100%;
}
@media (min-width: 1024px) {
  .hero-grid {
    grid-template-columns: 1fr 1fr;
    gap: 5rem;
  }
}

.hero-text {
  display: flex;
  flex-direction: column;
  gap: 1.4rem;
}

.hero-tag {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  font-size: 0.7rem;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: rgba(0, 0, 0, 0.55);
  opacity: 0;
  transform: translateY(20px);
}
.dot {
  width: 6px;
  height: 6px;
  background: #1a1a1a;
  border-radius: 50%;
  display: inline-block;
  animation: pulse 2s ease-in-out infinite;
}
@keyframes pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.3;
  }
}

.hero-heading {
  display: flex;
  flex-direction: column;
  font-weight: 700;
  letter-spacing: -0.04em;
  line-height: 0.88;
  text-transform: uppercase;
}
.hero-name-1 {
  font-size: clamp(3.5rem, 9vw, 7.5rem);

  transform: translateY(40px);
}
.hero-name-2 {
  font-size: clamp(2.8rem, 7.5vw, 6.5rem);

  transform: translateY(40px);
}

.hero-desc {
  font-size: 1rem;
  line-height: 1.75;
  color: rgba(0, 0, 0, 0.55);
  max-width: 380px;
  opacity: 0;
  transform: translateY(20px);
}
.hero-desc em {
  font-style: normal;
  color: #1a1a1a;
  font-weight: 700;
}

.hero-actions {
  display: flex;
  align-items: center;
  gap: 1.8rem;
  opacity: 0;
  transform: translateY(20px);
}
.btn-primary {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  text-decoration: none;
  color: var(--c-amber);
  background: #1a1a1a;
  padding: 0.85rem 2rem;
  border-radius: 2px;
  transition:
    opacity 0.2s,
    transform 0.2s;
}
.btn-primary:hover {
  opacity: 0.85;
  transform: translateY(-2px);
}

.hero-socials {
  display: flex;
  gap: 0.8rem;
}
.social-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 38px;
  height: 38px;
  border: 1px solid rgba(0, 0, 0, 0.25);
  border-radius: 50%;
  color: rgba(0, 0, 0, 0.5);
  text-decoration: none;
  transition:
    border-color 0.2s,
    color 0.2s;
}
.social-icon:hover {
  border-color: #1a1a1a;
  color: #1a1a1a;
}

.hero-img-wrap {
  position: relative;
  z-index: 5;
  opacity: 0;
  transform: scale(0.96);
}
.hero-img-inner {
  position: relative;
  aspect-ratio: 3/4;
  border-radius: 8px;
  overflow: hidden;
  background: rgba(0, 0, 0, 0.1);
  max-width: 420px;
  margin: 0 auto;
  box-shadow: 0 24px 64px rgba(0, 0, 0, 0.28);
}
@media (min-width: 1024px) {
  .hero-img-inner {
    margin: 0;
  }
}
.hero-photo {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center 30%;
  transition: transform 0.6s ease;
}
.hero-img-inner:hover .hero-photo {
  transform: scale(1.04);
}

.hero-img-badge {
  position: absolute;
  bottom: 1.2rem;
  left: 1.2rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(0, 0, 0, 0.75);
  backdrop-filter: blur(8px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  padding: 0.45rem 0.8rem;
  border-radius: 2px;
  font-size: 0.62rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--c-white);
}
.badge-dot {
  width: 6px;
  height: 6px;
  background: #4caf50;
  border-radius: 50%;
  animation: pulse 2s ease-in-out infinite;
}
.hero-img-bg {
  position: absolute;
  inset: 16px 175px -35px 18px;
  background: rgba(0, 0, 0, 0.08);
  border-radius: 8px;
  z-index: -1;
}

.scroll-hint {
  position: absolute;
  bottom: 2rem;
  left: 2.5rem;
  display: flex;
  align-items: center;
  gap: 1rem;
  z-index: 5;
}
@media (min-width: 1024px) {
  .scroll-hint {
    left: 5rem;
  }
}
.scroll-hint span {
  font-size: 0.6rem;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: rgba(0, 0, 0, 0.4);
}
.scroll-line {
  width: 60px;
  height: 1px;
  background: rgba(0, 0, 0, 0.2);
  overflow: hidden;
}
.scroll-line-fill {
  height: 100%;
  width: 30%;
  background: #1a1a1a;
  animation: scrollLine 2s ease-in-out infinite;
}
@keyframes scrollLine {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(400%);
  }
}

/* ══════════════════════════════════════
   MARQUEE
══════════════════════════════════════ */
.marquee-section {
  border-top: 1px solid var(--c-border);
  border-bottom: 1px solid var(--c-border);
  padding: 1.1rem 0;
  overflow: hidden;
  background: var(--c-surface);
}
.skills-track-wrap {
  overflow: hidden;
}
.skills-track {
  display: flex;
  align-items: center;
  white-space: nowrap;
  width: max-content;
  animation: marquee 22s linear infinite;
}
@keyframes marquee {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-50%);
  }
}
.skill-chip {
  font-size: 0.68rem;
  font-weight: 700;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--c-muted);
  padding: 0 1.4rem;
  display: inline-flex;
  align-items: center;
  gap: 1.4rem;
  transition: color 0.2s;
}
.skill-chip:hover {
  color: var(--c-accent);
}
.skill-sep {
  color: var(--c-accent);
  opacity: 0.5;
}

/* ══════════════════════════════════════
   SCROLL HORIZONTAL
══════════════════════════════════════ */
.div-horizontal {
  display: flex;
  height: 100vh;
  overflow-x: hidden;
  will-change: transform;
}
.secao-horizontal {
  flex-shrink: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}
.s1 {
  background: #000000;
  border-left: 1px solid var(--c-surface);
  border-right: 1px solid var(--c-surface);
}
.s2 {
  background: #111111;
  border-left: 1px solid var(--c-border);
  border-right: 1px solid var(--c-border);
}
.s3 {
  background: #000000;
  border-left: 1px solid var(--c-surface);
  border-right: 1px solid var(--c-border);
}
.word-wrap {
  position: relative;
  display: inline-block;
}
.word-stroke,
.word-solid {
  font-weight: 700;
  font-size: clamp(4rem, 12vw, 10rem);
  letter-spacing: -0.04em;
  text-transform: uppercase;
  line-height: 1;
  display: block;
}
.word-stroke {
  -webkit-text-stroke: 1px rgba(212, 168, 71, 0.2);
  color: transparent;
  position: absolute;
  top: 8px;
  left: 8px;
}
.word-solid {
  color: var(--c-text);
  opacity: 0.18;
}
.s2 .word-solid {
  color: var(--c-accent);
  opacity: 0.18;
}

/* ══════════════════════════════════════
   PROJETOS
══════════════════════════════════════ */
.projects-section {
  padding: 6rem 2rem 8rem;
  border-top: 1px solid var(--c-border);
}
@media (min-width: 1024px) {
  .projects-section {
    padding: 8rem 5rem 10rem;
  }
}

.projects-header {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  margin-bottom: 5rem;
  padding-bottom: 3rem;
  border-bottom: 1px solid var(--c-border);
}
@media (min-width: 768px) {
  .projects-header {
    flex-direction: row;
    align-items: flex-end;
    justify-content: space-between;
  }
}

.section-label {
  font-size: 0.65rem;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--c-accent);
  display: block;
  margin-bottom: 1rem;
}
.section-title {
  font-size: clamp(2.5rem, 6vw, 4.5rem);
  font-weight: 700;
  letter-spacing: -0.04em;
  line-height: 0.95;
  text-transform: uppercase;
  color: var(--c-white);
}
.title-muted {
  color: var(--c-muted);
}
.projects-header-desc {
  font-size: 0.85rem;
  line-height: 1.8;
  color: var(--c-muted);
  max-width: 280px;
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
@media (min-width: 768px) {
  .projects-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
@media (min-width: 1200px) {
  .projects-grid {
    grid-template-columns: repeat(3, 1fr);
    gap: 2rem;
  }
}

.project-card {
  cursor: none;
}
.project-img-wrap {
  position: relative;
  aspect-ratio: 16 / 10; /* aspect-ratio: 3/4; (para formato alto)*/
  overflow: hidden;
  border-radius: 12px;
  background: var(--c-surface);
  background-color: #f0f0f0;

  display: flex;
  align-items: center;
  justify-content: center;
}
.project-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: grayscale(60%);
  transition:
    filter 0.7s ease,
    transform 0.7s ease;
}
.project-card:hover .project-img {
  filter: grayscale(0%);
  transform: scale(1.05);
}
.project-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: flex-end;
  padding: 1.5rem;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.8) 0%, transparent 60%);
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
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--c-white);
}
.project-info {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  padding-top: 1rem;
  border-top: 1px solid var(--c-border);
}
.project-info-left {
  display: flex;
  align-items: flex-start;
  gap: 1rem;
}
.project-index {
  font-size: 0.6rem;
  color: var(--c-muted);
  letter-spacing: 0.1em;
  padding-top: 0.2rem;
  font-family: "Courier New", monospace;
}
.project-cat {
  font-size: 0.6rem;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--c-accent);
  margin-bottom: 0.3rem;
}
.project-title {
  font-size: 1rem;
  font-weight: 700;
  letter-spacing: -0.02em;
  text-transform: uppercase;
  color: var(--c-white);
}
.project-year {
  font-size: 0.6rem;
  color: var(--c-muted);
  font-family: "Courier New", monospace;
  padding-top: 0.1rem;
}

/* ══════════════════════════════════════
   CTA + PÁSSAROS
══════════════════════════════════════ */
.cta-section-wrap {
  position: relative;
  width: 100%;
  min-height: 85vh;
  background: var(--c-amber);
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  cursor: none;
  padding: 6rem 2rem;
  user-select: none;
}
@media (min-width: 1024px) {
  .cta-section-wrap {
    padding: 8rem 5rem;
    min-height: 80vh;
  }
}

.cta-inner {
  position: relative;
  z-index: 5;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
}
.cta-label {
  color: rgba(0, 0, 0, 0.5);
}
.cta-title {
  font-weight: 700;
  font-size: clamp(3rem, 10vw, 8rem);
  letter-spacing: -0.04em;
  line-height: 0.9;
  text-transform: uppercase;
  color: #1a1a1a;
}
.cta-outline {
  -webkit-text-stroke: 2px #1a1a1a;
  color: transparent;
}
.cta-email {
  display: inline-flex;
  align-items: center;
  gap: 0.6rem;
  font-size: clamp(1rem, 2.5vw, 1.4rem);
  font-weight: 700;
  color: #1a1a1a;
  text-decoration: none;
  border-bottom: 2px solid rgba(0, 0, 0, 0.3);
  padding-bottom: 0.2rem;
  transition: border-color 0.2s;
}
.cta-email:hover {
  border-color: #1a1a1a;
}
.cta-arrow {
  transition: transform 0.2s;
}
.cta-email:hover .cta-arrow {
  transform: translate(3px, -3px);
}
.cta-socials {
  display: flex;
  gap: 2rem;
}
.cta-social-link {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: rgba(0, 0, 0, 0.6);
  text-decoration: none;
  transition: color 0.2s;
}
.cta-social-link:hover {
  color: #1a1a1a;
}
.cta-bg-text {
  position: absolute;
  bottom: -0.15em;
  right: -0.05em;
  font-size: clamp(8rem, 28vw, 20rem);
  font-weight: 700;
  letter-spacing: -0.05em;
  color: rgba(0, 0, 0, 0.06);
  pointer-events: none;
  line-height: 1;
  user-select: none;
  z-index: 1;
}

/* ══════════════════════════════════════
   FOOTER
══════════════════════════════════════ */
.site-footer {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1.5rem 2.5rem;
  border-top: 1px solid var(--c-border);
  font-size: 0.65rem;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--c-muted);
}
@media (min-width: 1024px) {
  .site-footer {
    padding: 1.5rem 5rem;
  }
}
</style>
