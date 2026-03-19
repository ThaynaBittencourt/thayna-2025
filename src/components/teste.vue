<template>
  <Layout>
    <!-- Custom Cursor -->
    <div class="cursor" ref="cursor"></div>
    <div class="cursor-follower" ref="cursorFollower"></div>

    <!-- Noise Overlay -->
    <div class="noise-overlay"></div>

    <!-- Navigation -->
    <nav class="nav" ref="nav">
      <div class="nav-logo" @click="scrollTo('hero')">
        <span class="logo-bracket">&lt;</span>
        <span class="logo-name">Dev</span>
        <span class="logo-bracket">/&gt;</span>
      </div>
      <div class="nav-links">
        <a
          v-for="item in navItems"
          :key="item.id"
          @click="scrollTo(item.id)"
          class="nav-link"
        >
          <span class="nav-link-num">{{ item.num }}</span>
          <span class="nav-link-text">{{ item.label }}</span>
        </a>
      </div>
      <div class="nav-cta">
        <a href="mailto:dev@example.com" class="btn-hire">Hire Me</a>
      </div>
    </nav>

    <!-- Hero Section -->
    <section class="hero" id="hero" ref="heroSection">
      <div class="hero-bg-text">DEVELOPER</div>

      <div class="hero-content">
        <div class="hero-left">
          <p class="hero-greeting" ref="heroGreeting">Hello, I'm</p>
          <h1 class="hero-name" ref="heroName">
            <span class="name-line">Your</span>
            <span class="name-line accent">Name</span>
          </h1>
          <div class="hero-title" ref="heroTitle">
            <span class="title-text">Full Stack Developer</span>
            <span class="title-separator"> — </span>
            <span class="title-sub">Vue · Laravel · PHP</span>
          </div>
          <p class="hero-desc" ref="heroDesc">
            Crafting digital experiences that merge aesthetic precision with
            technical excellence. I build products that live at the intersection
            of design and engineering.
          </p>
          <div class="hero-actions" ref="heroActions">
            <a @click="scrollTo('work')" class="btn-primary">
              <span>View Work</span>
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
              >
                <path d="M5 12h14M12 5l7 7-7 7" />
              </svg>
            </a>
            <a @click="scrollTo('contact')" class="btn-secondary">Let's Talk</a>
          </div>
          <div class="hero-stats" ref="heroStats">
            <div v-for="stat in stats" :key="stat.label" class="stat-item">
              <span class="stat-num">{{ stat.num }}</span>
              <span class="stat-label">{{ stat.label }}</span>
            </div>
          </div>
        </div>
        <div class="hero-right" ref="heroRight">
          <div class="hero-image-wrapper">
            <div class="hero-image-ring ring-1"></div>
            <div class="hero-image-ring ring-2"></div>
            <div class="hero-image-frame">
              <img
                src="/images/eu_style.jpeg"
                alt="Profile"
                class="hero-photo"
              />
            </div>
            <div class="floating-badge badge-1">
              <span>⚡</span>
              <span>Available</span>
            </div>
            <div class="floating-badge badge-2">
              <span>🚀</span>
              <span>5+ Years</span>
            </div>
          </div>
        </div>
      </div>

      <div class="hero-scroll-hint" ref="scrollHint">
        <span>Scroll</span>
        <div class="scroll-line"></div>
      </div>

      <div class="hero-socials">
        <a
          v-for="social in socials"
          :key="social.name"
          :href="social.url"
          target="_blank"
          class="social-link"
        >
          <span v-html="social.icon"></span>
        </a>
      </div>
    </section>

    <!-- About Section -->
    <section class="about" id="about" ref="aboutSection">
      <div class="section-header">
        <span class="section-tag">01 — About</span>
        <h2 class="section-title" ref="aboutTitle">Who I Am</h2>
      </div>
      <div class="about-grid">
        <div class="about-text" ref="aboutText">
          <p>
            I'm a <strong>Full Stack Developer</strong> passionate about
            creating seamless digital experiences. With expertise spanning from
            pixel-perfect frontends to robust backend architectures, I bring
            ideas to life with clean, efficient code.
          </p>
          <p>
            My approach combines technical rigor with creative thinking — every
            line of code is an opportunity to solve a real problem elegantly.
          </p>
          <div class="about-image-small">
            <img src="/images/eu_preto_e_branco.jpeg" alt="Developer" />
            <div class="about-image-overlay"></div>
          </div>
        </div>
        <div class="about-skills" ref="aboutSkills">
          <h3>Tech Stack</h3>
          <div class="skills-grid">
            <div
              v-for="skill in skills"
              :key="skill.name"
              class="skill-card"
              :style="{ '--skill-color': skill.color }"
            >
              <div class="skill-icon" v-html="skill.icon"></div>
              <span class="skill-name">{{ skill.name }}</span>
              <div class="skill-bar">
                <div
                  class="skill-fill"
                  :style="{ width: skill.level + '%' }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Services Section -->
    <section class="services" id="services" ref="servicesSection">
      <div class="section-header">
        <span class="section-tag">02 — Services</span>
        <h2 class="section-title">What I Do</h2>
      </div>
      <div class="services-grid" ref="servicesGrid">
        <div
          v-for="(service, i) in services"
          :key="service.title"
          class="service-card"
          :ref="(el) => (serviceCards[i] = el)"
        >
          <div class="service-num">{{ String(i + 1).padStart(2, "0") }}</div>
          <div class="service-icon" v-html="service.icon"></div>
          <h3 class="service-title">{{ service.title }}</h3>
          <p class="service-desc">{{ service.desc }}</p>
          <div class="service-tags">
            <span v-for="tag in service.tags" :key="tag" class="service-tag">{{
              tag
            }}</span>
          </div>
        </div>
      </div>
    </section>

    <!-- Work Section -->
    <section class="work" id="work" ref="workSection">
      <div class="section-header">
        <span class="section-tag">03 — Portfolio</span>
        <h2 class="section-title">Selected Work</h2>
      </div>
      <div class="work-list">
        <div
          v-for="(project, i) in projects"
          :key="project.title"
          class="project-item"
          :ref="(el) => (projectItems[i] = el)"
          @mouseenter="onProjectHover(i)"
          @mouseleave="onProjectLeave()"
        >
          <div class="project-meta">
            <span class="project-num">{{
              String(i + 1).padStart(2, "0")
            }}</span>
            <span class="project-category">{{ project.category }}</span>
          </div>
          <div class="project-info">
            <h3 class="project-title">{{ project.title }}</h3>
            <p class="project-desc">{{ project.desc }}</p>
          </div>
          <div class="project-tags">
            <span v-for="tag in project.tags" :key="tag" class="project-tag">{{
              tag
            }}</span>
          </div>
          <a :href="project.url" target="_blank" class="project-link">
            <svg
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                d="M18 13v6a2 2 0 01-2 2H5a2 2 0 01-2-2V8a2 2 0 012-2h6M15 3h6v6M10 14L21 3"
              />
            </svg>
          </a>
          <div class="project-line"></div>
        </div>
      </div>

      <!-- Hover preview -->
      <div class="project-preview" ref="projectPreview">
        <div class="preview-inner" ref="previewInner">
          <div
            v-for="(project, i) in projects"
            :key="project.title"
            class="preview-img"
            :class="{ active: hoveredProject === i }"
          >
            <div
              class="preview-placeholder"
              :style="{ background: project.color }"
            >
              <span>{{ project.title }}</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Contact Section -->
    <section class="contact" id="contact" ref="contactSection">
      <div class="contact-bg-text">CONTACT</div>
      <div class="section-header">
        <span class="section-tag">04 — Contact</span>
        <h2 class="section-title contact-title" ref="contactTitle">
          Let's Work<br /><em>Together</em>
        </h2>
      </div>
      <div class="contact-grid">
        <div class="contact-info" ref="contactInfo">
          <p class="contact-intro">
            Have a project in mind? I'd love to hear about it. Let's create
            something extraordinary together.
          </p>
          <div class="contact-details">
            <div
              v-for="detail in contactDetails"
              :key="detail.label"
              class="contact-detail"
            >
              <span class="detail-label">{{ detail.label }}</span>
              <a :href="detail.href" class="detail-value">{{ detail.value }}</a>
            </div>
          </div>
        </div>
        <div class="contact-form-wrap" ref="contactForm">
          <form @submit.prevent="submitForm" class="contact-form">
            <div class="form-group">
              <input
                v-model="form.name"
                type="text"
                placeholder="Your Name"
                required
              />
              <div class="form-line"></div>
            </div>
            <div class="form-group">
              <input
                v-model="form.email"
                type="email"
                placeholder="Email Address"
                required
              />
              <div class="form-line"></div>
            </div>
            <div class="form-group">
              <input
                v-model="form.subject"
                type="text"
                placeholder="Project Subject"
              />
              <div class="form-line"></div>
            </div>
            <div class="form-group">
              <textarea
                v-model="form.message"
                placeholder="Tell me about your project..."
                rows="4"
                required
              ></textarea>
              <div class="form-line"></div>
            </div>
            <button type="submit" class="btn-send">
              <span>Send Message</span>
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
              >
                <path d="M22 2L11 13M22 2l-7 20-4-9-9-4 20-7z" />
              </svg>
            </button>
          </form>
        </div>
      </div>
    </section>

    <!-- Footer -->
    <footer class="footer" ref="footer">
      <div class="footer-top">
        <div class="footer-logo">
          <span class="logo-bracket">&lt;</span>
          <span class="logo-name">Dev</span>
          <span class="logo-bracket">/&gt;</span>
        </div>
        <div class="footer-socials">
          <a
            v-for="social in socials"
            :key="social.name"
            :href="social.url"
            target="_blank"
          >
            <span v-html="social.icon"></span>
          </a>
        </div>
      </div>
      <div class="footer-bottom">
        <span>© 2025 — Crafted with Vue & Laravel</span>
        <span>Full Stack Developer</span>
      </div>
    </footer>
  </Layout>
</template>

<script setup>
import { ref, onMounted, reactive } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import Layout from "@/Layouts/AppLayout.vue";

gsap.registerPlugin(ScrollTrigger);

// Refs
const cursor = ref(null);
const cursorFollower = ref(null);
const nav = ref(null);
const heroSection = ref(null);
const heroGreeting = ref(null);
const heroName = ref(null);
const heroTitle = ref(null);
const heroDesc = ref(null);
const heroActions = ref(null);
const heroStats = ref(null);
const heroRight = ref(null);
const scrollHint = ref(null);
const aboutTitle = ref(null);
const aboutText = ref(null);
const aboutSkills = ref(null);
const servicesGrid = ref(null);
const serviceCards = ref([]);
const projectItems = ref([]);
const projectPreview = ref(null);
const previewInner = ref(null);
const contactTitle = ref(null);
const contactInfo = ref(null);
const contactForm = ref(null);
const footer = ref(null);

const hoveredProject = ref(null);

const form = reactive({ name: "", email: "", subject: "", message: "" });

const navItems = [
  { id: "about", label: "About", num: "01" },
  { id: "services", label: "Services", num: "02" },
  { id: "work", label: "Work", num: "03" },
  { id: "contact", label: "Contact", num: "04" },
];

const stats = [
  { num: "5+", label: "Years Exp." },
  { num: "30+", label: "Projects" },
  { num: "20+", label: "Clients" },
];

const socials = [
  {
    name: "GitHub",
    url: "#",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M12 0C5.374 0 0 5.373 0 12c0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23A11.509 11.509 0 0112 5.803c1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576C20.566 21.797 24 17.3 24 12c0-6.627-5.373-12-12-12z"/></svg>',
  },
  {
    name: "LinkedIn",
    url: "#",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433a2.062 2.062 0 01-2.063-2.065 2.064 2.064 0 112.063 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"/></svg>',
  },
  {
    name: "Twitter",
    url: "#",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-4.714-6.231-5.401 6.231H2.744l7.737-8.835L1.254 2.25H8.08l4.713 6.231zm-1.161 17.52h1.833L7.084 4.126H5.117z"/></svg>',
  },
];

const skills = [
  {
    name: "Vue.js",
    level: 95,
    color: "#42b883",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M24 1.61h-9.94L12 5.16 9.94 1.61H0l12 20.78zM2.88 3.13h2.82l6.3 10.91L18.3 3.13h2.82L12 18.17z"/></svg>',
  },
  {
    name: "Laravel",
    level: 90,
    color: "#ff2d20",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M23.642 5.43a.364.364 0 01.014.1v5.149a.366.366 0 01-.19.314l-4.323 2.432v4.837a.364.364 0 01-.189.314l-9.093 5.11a.365.365 0 01-.378 0l-9.086-5.106a.365.365 0 01-.191-.315V5.144a.363.363 0 01.02-.105.378.378 0 01.015-.047c.014-.03.03-.06.051-.086l.01-.008.013-.018a.365.365 0 01.08-.068L9.58.113a.367.367 0 01.378 0l4.533 2.547.012.007a.366.366 0 01.175.316v4.627l3.965-2.229V.634a.366.366 0 01.19-.316l4.533-2.547a.365.365 0 01.377 0l4.534 2.547.01.006a.365.365 0 01.185.31"/></svg>',
  },
  {
    name: "JavaScript",
    level: 92,
    color: "#f7df1e",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M0 0h24v24H0V0zm22.034 18.276c-.175-1.095-.888-2.015-3.003-2.873-.736-.345-1.554-.585-1.797-1.14-.091-.33-.105-.51-.046-.705.15-.646.915-.84 1.515-.66.39.12.75.42.976.9 1.034-.676 1.034-.676 1.755-1.125-.27-.42-.404-.601-.586-.78-.63-.705-1.469-1.065-2.834-1.034l-.705.089c-.676.165-1.32.525-1.71 1.005-1.14 1.291-.811 3.541.569 4.471 1.365 1.02 3.361 1.244 3.616 2.205.24 1.17-.87 1.545-1.966 1.41-.811-.18-1.26-.586-1.755-1.336l-1.83 1.051c.21.48.45.689.81 1.109 1.74 1.756 6.09 1.666 6.871-1.004.029-.09.24-.705.074-1.65l.046.067zm-8.983-7.245h-2.248c0 1.938-.009 3.864-.009 5.805 0 1.232.063 2.363-.138 2.711-.33.689-1.18.601-1.566.48-.396-.196-.597-.466-.83-.855-.063-.105-.11-.196-.127-.196l-1.825 1.125c.305.63.75 1.172 1.324 1.517.855.51 2.004.675 3.207.405.783-.226 1.458-.691 1.811-1.411.51-.93.402-2.07.397-3.346.012-2.054 0-4.109 0-6.179l.004-.056z"/></svg>',
  },
  {
    name: "PHP",
    level: 85,
    color: "#777bb4",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M7.01 10.207h-.944l-.515 2.648h.838c.556 0 .97-.105 1.242-.314.272-.21.455-.559.55-1.049.092-.47.05-.802-.124-.995-.175-.193-.527-.29-1.047-.29zM12 5.688C5.373 5.688 0 8.514 0 12s5.373 6.313 12 6.313S24 15.486 24 12c0-3.486-5.373-6.312-12-6.312zm-3.26 7.451c-.261.25-.575.438-.917.551-.336.108-.765.164-1.285.164H5.357l-.327 1.681H3.652l1.23-6.326h2.65c.797 0 1.378.209 1.744.628.366.418.476 1.002.33 1.752a2.836 2.836 0 01-.555 1.115 2.84 2.84 0 01-.311.435zm4.312 2.396l.77-3.861c.033-.134.048-.24.048-.33 0-.196-.066-.324-.199-.387-.148-.066-.366-.1-.657-.1h-.801l-.921 4.678h-1.37l1.23-6.326h3.6c.473 0 .851.073 1.133.22.281.148.469.365.562.651.094.287.11.599.044.937l-.793 4.518h-1.646zm6.958-.006h-1.496l-.063-.517c-.391.307-.777.517-1.159.63-.376.109-.771.165-1.184.165-.503 0-.904-.132-1.203-.397s-.438-.617-.416-1.058c.034-.696.313-1.192.838-1.493.527-.3 1.336-.46 2.428-.479l.854-.006.017-.037c.043-.188.01-.33-.097-.423-.107-.094-.295-.14-.564-.14-.26 0-.548.044-.864.132a4.79 4.79 0 00-.888.367l.256-1.238c.313-.112.636-.2.97-.265.333-.064.65-.096.953-.096.688 0 1.2.141 1.537.422.336.282.468.698.396 1.246l-.451 2.578a2.54 2.54 0 01-.012.17z"/></svg>',
  },
  {
    name: "CSS3",
    level: 93,
    color: "#264de4",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M1.5 0h21l-1.91 21.563L11.977 24l-8.564-2.438L1.5 0zm17.09 4.413L5.41 4.41l.213 2.622 10.125.002-.255 2.716h-6.64l.24 2.573h6.182l-.366 3.523-2.91.804-2.956-.81-.188-2.11h-2.61l.29 3.855L12 19.288l5.373-1.53L18.59 4.414z"/></svg>',
  },
  {
    name: "HTML5",
    level: 98,
    color: "#e34f26",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M1.5 0h21l-1.91 21.563L11.977 24l-8.565-2.438L1.5 0zm7.031 9.75l-.232-2.718 10.059.003.23-2.622L5.412 4.41l.698 8.01h9.126l-.326 3.426-2.91.804-2.955-.81-.188-2.11H6.248l.33 4.171L12 19.351l5.379-1.443.744-8.157H8.531z"/></svg>',
  },
  {
    name: "Bootstrap",
    level: 88,
    color: "#7952b3",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M11.77 11.24H9.956V8.202h2.152c1.17 0 1.834.522 1.834 1.466 0 1.008-.773 1.572-2.174 1.572zm.324 1.206H9.956v3.348h2.231c1.459 0 2.232-.585 2.232-1.685s-.795-1.663-2.325-1.663zM24 11.39v1.218c-1.128.108-1.817.944-2.226 2.268-.407 1.319-.463 2.937-.42 4.186.045 1.3-.968 2.5-2.337 2.5H4.985c-1.37 0-2.383-1.2-2.337-2.5.043-1.249-.013-2.867-.42-4.186-.41-1.324-1.1-2.16-2.228-2.268V11.39c1.128-.108 1.819-.944 2.227-2.268.408-1.319.464-2.937.42-4.186C2.603 3.636 3.616 2.436 4.985 2.436h14.032c1.37 0 2.382 1.2 2.337 2.5-.043 1.249.012 2.867.42 4.186.409 1.324 1.098 2.16 2.226 2.268zm-7.927 2.817c0-1.354-.953-2.333-2.368-2.488v-.057c1.04-.195 1.877-1.128 1.877-2.252 0-1.615-1.254-2.607-3.351-2.607H8.586v10.598h3.797c2.26 0 3.69-1.006 3.69-3.194z"/></svg>',
  },
  {
    name: "MySQL",
    level: 82,
    color: "#4479a1",
    icon: '<svg viewBox="0 0 24 24" fill="currentColor"><path d="M16.405 5.501c-.115 0-.193.014-.274.033v.013h.014c.054.104.146.18.214.273.054.107.1.214.154.32l.014-.015c.094-.066.14-.172.14-.333-.04-.047-.046-.094-.08-.14-.04-.067-.126-.1-.18-.15zM5.77 18.695h-.927a50.854 50.854 0 00-.27-4.41h-.008l-1.41 4.41H2.45l-1.4-4.41h-.01a72.892 72.892 0 00-.195 4.41H0c.055-1.966.192-3.81.41-5.53h1.15l1.335 4.064h.008l1.347-4.064h1.095c.242 2.015.384 3.86.428 5.53zm4.017-4.08h-2.586v1.542h2.145v.92h-2.145v1.766h2.586v.954h-3.6v-6.16h3.6v.978zm1.487 4.08h-1.014v-6.16h1.014v6.16zm3.274-4.22c-.527-.19-.946-.397-1.258-.618-.312-.22-.468-.507-.468-.862 0-.405.18-.73.54-.976.36-.245.845-.367 1.456-.367.51 0 1.012.1 1.508.3l-.198.91c-.41-.2-.842-.3-1.298-.3-.238 0-.426.047-.565.14-.14.093-.21.216-.21.37 0 .14.064.255.193.346.128.09.33.18.603.267.614.21 1.064.44 1.35.69.286.252.43.57.43.955 0 .42-.195.76-.585 1.017-.392.257-.922.386-1.59.386-.62 0-1.18-.128-1.677-.38l.214-.935c.47.25 1.003.376 1.596.376.275 0 .492-.057.65-.17.158-.115.237-.268.237-.457 0-.166-.076-.304-.228-.415-.152-.11-.41-.228-.77-.354zM23.695 18.695h-1.014v-6.16h1.014v6.16z"/></svg>',
  },
];

const services = [
  {
    title: "Frontend Development",
    desc: "Building responsive, performant interfaces with Vue.js, crafting pixel-perfect experiences that delight users.",
    icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M9 17.25v1.007a3 3 0 01-.879 2.122L7.5 21h9l-.621-.621A3 3 0 0115 18.257V17.25m6-12V15a2.25 2.25 0 01-2.25 2.25H5.25A2.25 2.25 0 013 15V5.25m18 0A2.25 2.25 0 0018.75 3H5.25A2.25 2.25 0 003 5.25m18 0H3"/></svg>',
    tags: ["Vue.js", "Inertia", "GSAP", "Tailwind"],
    color: "#42b883",
  },
  {
    title: "Backend Development",
    desc: "Architecting scalable APIs and robust server-side logic with Laravel, ensuring performance and security.",
    icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M6.429 9.75L2.25 12l4.179 2.25m0-4.5l5.571 3 5.571-3m-11.142 0L2.25 7.5 12 2.25l9.75 5.25-4.179 2.25m0 0L21.75 12l-4.179 2.25m0 0l4.179 2.25L12 21.75 2.25 16.5l4.179-2.25m11.142 0l-5.571 3-5.571-3"/></svg>',
    tags: ["Laravel", "PHP", "MySQL", "REST API"],
    color: "#ff2d20",
  },
  {
    title: "UI/UX Design",
    desc: "Translating ideas into intuitive interfaces with thoughtful UX patterns and visually compelling aesthetics.",
    icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M9.53 16.122a3 3 0 00-5.78 1.128 2.25 2.25 0 01-2.4 2.245 4.5 4.5 0 008.4-2.245c0-.399-.078-.78-.22-1.128zm0 0a15.998 15.998 0 003.388-1.62m-5.043-.025a15.994 15.994 0 011.622-3.395m3.42 3.42a15.995 15.995 0 004.764-4.648l3.876-5.814a1.151 1.151 0 00-1.597-1.597L14.146 6.32a15.996 15.996 0 00-4.649 4.763m3.42 3.42a6.776 6.776 0 00-3.42-3.42"/></svg>',
    tags: ["Figma", "Prototyping", "Design Systems"],
    color: "#f7df1e",
  },
  {
    title: "Performance Optimization",
    desc: "Analyzing and optimizing web applications for speed, accessibility, and outstanding Core Web Vitals.",
    icon: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M3.75 13.5l10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75z"/></svg>',
    tags: ["Lighthouse", "WebVitals", "CDN", "Caching"],
    color: "#7952b3",
  },
];

const projects = [
  {
    title: "E-Commerce Platform",
    category: "Web Application",
    desc: "Full-stack marketplace with real-time inventory, payment integration, and admin dashboard.",
    tags: ["Laravel", "Vue.js", "Stripe"],
    url: "#",
    color: "linear-gradient(135deg, #1a1a2e 0%, #16213e 100%)",
  },
  {
    title: "SaaS Dashboard",
    category: "Dashboard",
    desc: "Analytics platform with interactive charts, team management and subscription billing.",
    tags: ["Inertia", "Laravel", "Chart.js"],
    url: "#",
    color: "linear-gradient(135deg, #0f2027 0%, #203a43 100%)",
  },
  {
    title: "Portfolio System",
    category: "CMS",
    desc: "Dynamic portfolio CMS with drag-and-drop builder and multi-tenant support.",
    tags: ["Vue.js", "PHP", "MySQL"],
    url: "#",
    color: "linear-gradient(135deg, #1a0533 0%, #2d1b69 100%)",
  },
  {
    title: "Real Estate App",
    category: "Mobile-first Web",
    desc: "Property listing platform with map integration, filtering and virtual tour support.",
    tags: ["Laravel", "Vue.js", "Maps API"],
    url: "#",
    color: "linear-gradient(135deg, #0d2137 0%, #1a4d6b 100%)",
  },
];

const contactDetails = [
  { label: "Email", value: "dev@example.com", href: "mailto:dev@example.com" },
  { label: "Phone", value: "+55 84 99999-0000", href: "tel:+5584999990000" },
  { label: "Location", value: "Natal, RN — Brazil", href: "#" },
];

function scrollTo(id) {
  document.getElementById(id)?.scrollIntoView({ behavior: "smooth" });
}

function onProjectHover(i) {
  hoveredProject.value = i;
  gsap.to(projectPreview.value, { opacity: 1, duration: 0.3 });
}
function onProjectLeave() {
  hoveredProject.value = null;
  gsap.to(projectPreview.value, { opacity: 0, duration: 0.3 });
}

function submitForm() {
  console.log("Form submitted", form);
}

onMounted(() => {
  // Custom cursor
  window.addEventListener("mousemove", (e) => {
    gsap.to(cursor.value, { x: e.clientX, y: e.clientY, duration: 0 });
    gsap.to(cursorFollower.value, {
      x: e.clientX,
      y: e.clientY,
      duration: 0.15,
    });
  });

  document.querySelectorAll("a, button, .project-item").forEach((el) => {
    el.addEventListener("mouseenter", () => {
      cursor.value?.classList.add("cursor-hover");
      cursorFollower.value?.classList.add("cursor-hover");
    });
    el.addEventListener("mouseleave", () => {
      cursor.value?.classList.remove("cursor-hover");
      cursorFollower.value?.classList.remove("cursor-hover");
    });
  });

  // Project preview follow mouse
  window.addEventListener("mousemove", (e) => {
    if (hoveredProject.value !== null) {
      gsap.to(projectPreview.value, {
        x: e.clientX + 20,
        y: e.clientY - 80,
        duration: 0.4,
        ease: "power2.out",
      });
    }
  });

  // Nav scroll effect
  ScrollTrigger.create({
    start: "top -80",
    onEnter: () => nav.value?.classList.add("nav-scrolled"),
    onLeaveBack: () => nav.value?.classList.remove("nav-scrolled"),
  });

  // Hero animations
  const heroTl = gsap.timeline({ delay: 0.3 });
  heroTl
    .from(heroGreeting.value, {
      y: 30,
      opacity: 0,
      duration: 0.8,
      ease: "power3.out",
    })
    .from(
      heroName.value.children,
      { y: 60, opacity: 0, duration: 0.9, stagger: 0.1, ease: "power3.out" },
      "-=0.4",
    )
    .from(
      heroTitle.value,
      { y: 20, opacity: 0, duration: 0.7, ease: "power3.out" },
      "-=0.5",
    )
    .from(
      heroDesc.value,
      { y: 20, opacity: 0, duration: 0.7, ease: "power3.out" },
      "-=0.4",
    )
    .from(
      heroActions.value.children,
      { y: 20, opacity: 0, duration: 0.6, stagger: 0.1, ease: "power3.out" },
      "-=0.3",
    )
    .from(
      heroStats.value.children,
      { y: 20, opacity: 0, duration: 0.5, stagger: 0.1, ease: "power3.out" },
      "-=0.3",
    )
    .from(
      heroRight.value,
      { x: 60, opacity: 0, duration: 1, ease: "power3.out" },
      "-=1.2",
    )
    .from(scrollHint.value, { opacity: 0, duration: 0.5 }, "-=0.2");

  // Floating badges animation
  gsap.to(".badge-1", {
    y: -10,
    duration: 2,
    repeat: -1,
    yoyo: true,
    ease: "power1.inOut",
  });
  gsap.to(".badge-2", {
    y: 10,
    duration: 2.5,
    repeat: -1,
    yoyo: true,
    ease: "power1.inOut",
  });

  // Ring animation
  gsap.to(".ring-1", {
    rotation: 360,
    duration: 20,
    repeat: -1,
    ease: "none",
    transformOrigin: "50% 50%",
  });
  gsap.to(".ring-2", {
    rotation: -360,
    duration: 15,
    repeat: -1,
    ease: "none",
    transformOrigin: "50% 50%",
  });

  // About section
  gsap.from(aboutTitle.value, {
    scrollTrigger: { trigger: aboutTitle.value, start: "top 80%" },
    y: 40,
    opacity: 0,
    duration: 0.8,
    ease: "power3.out",
  });
  gsap.from(aboutText.value, {
    scrollTrigger: { trigger: aboutText.value, start: "top 80%" },
    x: -50,
    opacity: 0,
    duration: 0.9,
    ease: "power3.out",
  });
  gsap.from(aboutSkills.value, {
    scrollTrigger: { trigger: aboutSkills.value, start: "top 80%" },
    x: 50,
    opacity: 0,
    duration: 0.9,
    ease: "power3.out",
  });

  // Skill bars
  document.querySelectorAll(".skill-fill").forEach((bar) => {
    const width = bar.style.width;
    bar.style.width = "0";
    ScrollTrigger.create({
      trigger: bar,
      start: "top 85%",
      onEnter: () => gsap.to(bar, { width, duration: 1.2, ease: "power2.out" }),
    });
  });

  // Services
  gsap.from(serviceCards.value, {
    scrollTrigger: { trigger: servicesGrid.value, start: "top 80%" },
    y: 60,
    opacity: 0,
    duration: 0.7,
    stagger: 0.15,
    ease: "power3.out",
  });

  // Projects
  projectItems.value.forEach((item, i) => {
    gsap.from(item, {
      scrollTrigger: { trigger: item, start: "top 85%" },
      x: -30,
      opacity: 0,
      duration: 0.7,
      delay: i * 0.1,
      ease: "power3.out",
    });
  });

  // Contact
  gsap.from(contactTitle.value, {
    scrollTrigger: { trigger: contactTitle.value, start: "top 80%" },
    y: 40,
    opacity: 0,
    duration: 0.8,
    ease: "power3.out",
  });
  gsap.from(contactInfo.value, {
    scrollTrigger: { trigger: contactInfo.value, start: "top 80%" },
    x: -40,
    opacity: 0,
    duration: 0.9,
    ease: "power3.out",
  });
  gsap.from(contactForm.value, {
    scrollTrigger: { trigger: contactForm.value, start: "top 80%" },
    x: 40,
    opacity: 0,
    duration: 0.9,
    ease: "power3.out",
  });
});
</script>
