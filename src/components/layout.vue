<script setup>
import { useI18n } from "vue-i18n";
import LanguageSwitcher from "@/components/language-switcher.vue";
import ShootingStars from "@/components/ShootingStars.vue";
import { ref, onMounted, onBeforeUnmount } from 'vue';

import vistaConsumidorImg from "@/assets/fotos/vista_consumidor.png";
import vistaProveedorImg from "@/assets/fotos/vista_provedor.png";
import vistaAdminImg from "@/assets/fotos/vista_administrador.png";
import arqSoftwareImg from "@/assets/fotos/arq_software.png";

const { t } = useI18n();

const projectSlides = [
  {
    key: "consumer",
    titleKey: "projects.mealflow.slides.consumer",
    image: vistaConsumidorImg,
  },
  {
    key: "provider",
    titleKey: "projects.mealflow.slides.provider",
    image: vistaProveedorImg,
  },
  {
    key: "admin",
    titleKey: "projects.mealflow.slides.admin",
    image: vistaAdminImg,
  },
  {
    key: "architecture",
    titleKey: "projects.mealflow.slides.architecture",
    image: arqSoftwareImg,
  },
];

const currentSlide = ref(0);
let slideTimer = null;

function startSlideTimer() {
  stopSlideTimer();
  slideTimer = setInterval(() => {
    currentSlide.value = (currentSlide.value + 1) % projectSlides.length;
  }, 3000);
}

function stopSlideTimer() {
  if (slideTimer) {
    clearInterval(slideTimer);
    slideTimer = null;
  }
}

function goToSlide(index) {
  currentSlide.value = index;
  startSlideTimer();
}

function prevSlide() {
  currentSlide.value =
    (currentSlide.value - 1 + projectSlides.length) % projectSlides.length;
  startSlideTimer();
}

function nextSlide() {
  currentSlide.value = (currentSlide.value + 1) % projectSlides.length;
  startSlideTimer();
}

function openGithub() {
  window.open('https://github.com/Gabrlel0105', '_blank', 'noopener,noreferrer');
}

// mobile menu state
const mobileOpen = ref(false);
function toggleMobile() {
  mobileOpen.value = !mobileOpen.value;
}
function closeMobile() {
  mobileOpen.value = false;
}

// click-outside for mobile menu
const headerRef = ref(null);
function handleDocClick(e) {
  if (!mobileOpen.value) return;
  const el = headerRef.value;
  if (!el) return;
  if (!el.contains(e.target)) {
    mobileOpen.value = false;
  }
}
function handleKeydown(e) {
  if (e.key === 'Escape' && mobileOpen.value) mobileOpen.value = false;
}

onMounted(() => {
  document.addEventListener('click', handleDocClick);
  document.addEventListener('keydown', handleKeydown);
  startSlideTimer();
});
onBeforeUnmount(() => {
  document.removeEventListener('click', handleDocClick);
  document.removeEventListener('keydown', handleKeydown);
  stopSlideTimer();
});
</script>

<template>
  <div class="layout">
    <!-- 🔹 Navbar -->
    <header ref="headerRef" class="navbar flex justify-content-between align-items-center px-5 py-3">
      <h2 class="logo">GM</h2>

      <nav
        class="flex align-items-center gap-4 nav-menu"
        :class="{ open: mobileOpen }"
      >
        <a href="#home" @click="closeMobile">01. {{ t("nav.home") }}</a>
        <a href="#about" @click="closeMobile">02. {{ t("nav.about") }}</a>
        <a href="#skills" @click="closeMobile">03. {{ t("nav.skills") }}</a>
        <a href="#projects" @click="closeMobile">04. {{ t("nav.projects") }}</a>
        <LanguageSwitcher />
      </nav>

      <!-- mobile menu toggle -->
      <button
        class="menu-toggle"
        @click="toggleMobile"
        :aria-expanded="mobileOpen.toString()"
        aria-label="Toggle navigation"
      >
        <svg class="hamburger-icon" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" aria-hidden="true" focusable="false">
          <rect class="line line1" x="3" y="6" width="18" height="2" rx="1" />
          <rect class="line line2" x="3" y="11" width="18" height="2" rx="1" />
          <rect class="line line3" x="3" y="16" width="18" height="2" rx="1" />
        </svg>
      </button>
    </header>

    <!-- 📌 Barra Lateral Izquierda Fija (Redes Sociales) -->
    <aside class="sidebar-left" aria-label="Social Links">
      <a
        href="https://github.com/Gabrlel0105"
        target="_blank"
        rel="noopener noreferrer"
        aria-label="GitHub"
        class="sidebar-icon-link"
      >
        <i class="pi pi-github"></i>
      </a>
      <a
        href="https://linkedin.com/in/gabrielmarca"
        target="_blank"
        rel="noopener noreferrer"
        aria-label="LinkedIn"
        class="sidebar-icon-link"
      >
        <i class="pi pi-linkedin"></i>
      </a>
      <a
        href="mailto:gabrielmarca.dev@outlook.com"
        aria-label="Outlook Email"
        class="sidebar-icon-link"
      >
        <i class="pi pi-envelope"></i>
      </a>
      <div class="sidebar-line"></div>
    </aside>

    <!-- 📌 Barra Lateral Derecha Fija (Correo de Outlook) -->
    <aside class="sidebar-right" aria-label="Contact Email">
      <a
        href="mailto:gabrielmarca.dev@outlook.com"
        class="sidebar-email-link"
      >
        gabrielmarca.dev@outlook.com
      </a>
      <div class="sidebar-line"></div>
    </aside>

    <!-- 🏠 Hero Section -->
    <section id="home" class="hero flex flex-column justify-content-center align-items-start">
      <ShootingStars />
      <p class="intro-text">{{ t("hero.greeting") }}</p>
      <h1 class="main-name">Gabriel Mamani Marca</h1>
      <h2 class="subtitle">{{ t("hero.subtitle") }}</h2>
      <p class="description">
        {{ t("hero.description") }}
      </p>
      <pv-button
        :label="t('hero.cta')"
        icon="pi pi-github"
        class="mt-4 resume-btn"
        outlined
        @click="openGithub"
      />
    </section>

    <!-- 👤 About Section -->
    <section id="about" class="section">
      <div class="about-container">
        <div class="about-content">
          <h2 class="section-title">02. {{ t("about.title") }}</h2>
          <p class="section-text">
            {{ t("about.p1") }}
          </p>
          <p class="section-text">
            {{ t("about.p2") }}
          </p>
          <p class="section-text">
            {{ t("about.p3") }}
          </p>
        </div>

        <div class="about-image-col">
          <div class="profile-img-wrapper">
            <img src="@/assets/foto.jpg" class="profile-img" alt="Gabriel Mamani Marca" />
          </div>
        </div>
      </div>
    </section>

    <section id="skills" class="section skills-section">
      <h2 class="skills-main-title text-center">
        <span class="title-number">03.</span> {{ t("skills.title") }}
      </h2>

      <div class="skills-grid">
        <!-- Frontend Column -->
        <div class="skills-column">
          <h3 class="skills-subtitle">
            <span class="triangle">▸</span> {{ t("skills.frontend") }} <span class="triangle">◂</span>
          </h3>
          <div class="tech-logos">
            <!-- Vue.js SVG -->
            <div class="tech-item" v-tooltip.top="'Vue.js'">
              <svg class="tech-logo" viewBox="0 0 128 128" xmlns="http://www.w3.org/2000/svg" aria-label="Vue.js">
                <path fill="#42b883" d="M78.8,10L64,35.4L49.2,10H0l64,110l64-110C128,10,78.8,10,78.8,10z"/>
                <path fill="#35495e" d="M78.8,10L64,35.4L49.2,10H25.6L64,76l38.4-66H78.8z"/>
              </svg>
              <span class="tech-name">Vue</span>
            </div>

            <div class="tech-item" v-tooltip.top="'React'">
              <svg class="tech-logo" viewBox="-11.5 -10.23174 23 20.46348" xmlns="http://www.w3.org/2000/svg" aria-label="React">
                <circle cx="0" cy="0" r="2.05" fill="#61dafb"/>
                <g stroke="#61dafb" stroke-width="1" fill="none">
                  <ellipse rx="11" ry="4.2"/>
                  <ellipse rx="11" ry="4.2" transform="rotate(60)"/>
                  <ellipse rx="11" ry="4.2" transform="rotate(120)"/>
                </g>
              </svg>
              <span class="tech-name">React</span>
            </div>
          </div>
        </div>

        <!-- Backend Column -->
        <div class="skills-column">
          <h3 class="skills-subtitle">
            <span class="triangle">▸</span> {{ t("skills.backend") }} <span class="triangle">◂</span>
          </h3>
          <div class="tech-logos">
            <!-- FastAPI SVG -->
            <div class="tech-item" v-tooltip.top="'FastAPI'">
              <svg class="tech-logo" viewBox="0 0 128 128" xmlns="http://www.w3.org/2000/svg" aria-label="FastAPI">
                <circle cx="64" cy="64" r="60" fill="#009688"/>
                <path fill="#ffffff" d="M68.5 22L34 70h27l-5.5 36L94 58H67l1.5-36z"/>
              </svg>
              <span class="tech-name">FastAPI</span>
            </div>

            <!-- Spring Boot SVG -->
            <div class="tech-item" v-tooltip.top="'Spring Boot'">
              <svg class="tech-logo" viewBox="0 0 128 128" xmlns="http://www.w3.org/2000/svg" aria-label="Spring">
                <path fill="#6DB33F" d="M116.5 18.2c-1.8 4.4-5.9 7.8-10.7 9.5C90.6 15.1 69.2 10.6 48.8 16.5 22.9 24.1 6.8 50.6 12.6 77c1.9 8.7 6 16.6 11.7 23.1l-4.2 3.7c-1.8 1.6-0.7 4.6 1.7 4.6h19.6c1.2 0 2.3-0.9 2.5-2.1 5.8 2.2 12.1 3.4 18.6 3.4 28.4 0 51.9-21.5 54.6-49.7 1.7-17.8-4.7-34.1-0.6-41.8zM37.3 95.3c-2.5 0-4.5-2-4.5-4.5s2-4.5 4.5-4.5 4.5 2 4.5 4.5-2 4.5-4.5 4.5zm68.1-27.7c-12.5 16.1-36.3 18.8-57 17.8 0 0-3.5-0.2-7-0.8 0 0 1.3-0.6 3-1.3 13.7-4.8 20.2-5.8 28.5-10.1 15.7-8 31.3-25.5 34.4-43.6-5.9 17.3-23.9 32.1-40.2 38.2-11.2 4.1-31.4 8.2-31.4 8.2l-0.8-0.4c-13.7-6.7-14.1-36.4 10.8-46 10.9-4.2 21.3-1.9 33.1-4.7 12.6-3 27.2-12.4 33.1-24.7 6.6 19.7 14.5 50.3-6.5 67.4z"/>
              </svg>
              <span class="tech-name">Spring</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Tarjeta de Certificados con PrimeVue pv-card -->
      <div class="certificates-wrapper">
        <pv-card class="certificates-card">
          <template #header>
            <div class="certificates-header">
              <i class="pi pi-trophy certificate-icon" aria-hidden="true"></i>
              <h3 class="certificates-title">
                <span class="triangle">▸</span> {{ t("skills.certificates") }} <span class="triangle">◂</span>
              </h3>
            </div>
          </template>
          <template #content>
            <ul class="certificates-list">
              <li>
                <a href="https://coursera.org/share/d87f4f400a5fa85bb057d1f08008a55b" target="_blank" rel="noopener noreferrer" class="certificate-link">
                  Databases for Data Scientists — University of Colorado Boulder
                </a>
              </li>
              <li>
                <a href="https://coursera.org/share/47e5167059a6346ced3e271eb193b773" target="_blank" rel="noopener noreferrer" class="certificate-link">
                  Python for Everybody — University of Michigan
                </a>
              </li>
            </ul>
          </template>
        </pv-card>
      </div>
    </section>

    <!-- 🚀 Projects Section -->
    <section id="projects" class="section projects-section">
      <h2 class="projects-main-title text-center">
        <span class="projects-number">04.</span> {{ t("projects.title") }}
      </h2>

      <div class="featured-project-grid">
        <!-- Columna Izquierda: Información del Proyecto -->
        <div class="project-info-col">
          <p class="project-overtitle">{{ t("projects.overtitle") }}</p>
          <h3 class="project-featured-title">
            {{ t("projects.mealflow.title") }}
          </h3>

          <div class="project-description-card">
            <p>
              <strong>MealFlow</strong> {{ t("projects.mealflow.p1_intro") }}
            </p>
            <p>
              {{ t("projects.mealflow.p2_1") }} <span class="highlight-tech">Java</span> {{ t("projects.mealflow.p2_and") }} <span class="highlight-tech">Spring Boot</span>{{ t("projects.mealflow.p2_2") }} <span class="highlight-tech">API Gateway</span>{{ t("projects.mealflow.p2_3") }} <span class="highlight-tech">Domain-Driven Design (DDD)</span> {{ t("projects.mealflow.p2_4") }} <span class="highlight-tech">Spring Security</span> {{ t("projects.mealflow.p2_5") }} <span class="highlight-tech">JWT</span>{{ t("projects.mealflow.p2_6") }} <span class="highlight-tech">Redis</span> {{ t("projects.mealflow.p2_7") }} <span class="highlight-tech">RabbitMQ</span> {{ t("projects.mealflow.p2_8") }}
            </p>
            <p>
              {{ t("projects.mealflow.p3_1") }} <span class="highlight-tech">Vue.js</span> {{ t("projects.mealflow.p2_and") }} <span class="highlight-tech">PrimeVue</span>{{ t("projects.mealflow.p3_2") }} <span class="highlight-tech">MySQL</span> {{ t("projects.mealflow.p3_3") }} <span class="highlight-tech">Azure</span> {{ t("projects.mealflow.p3_4") }}
            </p>
          </div>

          <ul class="project-tech-stack">
            <li>Java</li>
            <li>Spring Boot</li>
            <li>Vue.js</li>
            <li>PrimeVue</li>
            <li>MySQL</li>
            <li>Redis</li>
            <li>RabbitMQ</li>
            <li>Azure</li>
          </ul>

          <div class="project-links">
            <a
              href="https://github.com/Gabrlel0105"
              target="_blank"
              rel="noopener noreferrer"
              aria-label="GitHub Repository"
              class="project-icon-link"
            >
              <i class="pi pi-github"></i>
            </a>
            <a
              href="https://github.com/Gabrlel0105"
              target="_blank"
              rel="noopener noreferrer"
              aria-label="External Link"
              class="project-icon-link"
            >
              <i class="pi pi-external-link"></i>
            </a>
          </div>
        </div>

        <!-- Columna Derecha: Carrusel de Imágenes del Proyecto -->
        <div class="project-image-col">
          <div
            class="project-carousel"
            @mouseenter="stopSlideTimer"
            @mouseleave="startSlideTimer"
          >
            <!-- Título del Slide visible e i18n -->
            <div class="carousel-header">
              <div class="carousel-header-left">
                <span class="carousel-dot-indicator"></span>
                <span class="carousel-slide-title">
                  {{ t(projectSlides[currentSlide].titleKey) }}
                </span>
              </div>
              <div class="carousel-counter">
                {{ currentSlide + 1 }} / {{ projectSlides.length }}
              </div>
            </div>

            <!-- Visor del carrusel con imágenes -->
            <div class="carousel-viewport">
              <div
                v-for="(slide, index) in projectSlides"
                :key="slide.key"
                class="carousel-slide"
                :class="{ active: currentSlide === index }"
              >
                <img
                  :src="slide.image"
                  :alt="t(slide.titleKey)"
                  class="carousel-img"
                  loading="lazy"
                />
              </div>

              <!-- Flechas de navegación -->
              <button
                type="button"
                class="carousel-arrow prev"
                @click.stop="prevSlide"
                aria-label="Previous slide"
              >
                <i class="pi pi-chevron-left"></i>
              </button>
              <button
                type="button"
                class="carousel-arrow next"
                @click.stop="nextSlide"
                aria-label="Next slide"
              >
                <i class="pi pi-chevron-right"></i>
              </button>
            </div>

            <!-- Bolitas debajo del carrusel -->
            <div class="carousel-indicators">
              <button
                v-for="(slide, index) in projectSlides"
                :key="'dot-' + slide.key"
                type="button"
                class="carousel-dot"
                :class="{ active: currentSlide === index }"
                @click="goToSlide(index)"
                :aria-label="t(slide.titleKey)"
                :title="t(slide.titleKey)"
              ></button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ⚙️ Footer -->
    <footer class="footer text-center py-4">
      <small>© {{ new Date().getFullYear() }} Gabriel Marca — {{ t("footer.role") }}</small>
    </footer>
  </div>
</template>

<style scoped>
.layout {
  background-color: #0a192f;
  color: #ccd6f6;
  font-family: "Poppins", sans-serif;
  min-height: 100vh; /* ✅ asegura que la página ocupa toda la altura */
  display: flex;
  flex-direction: column;
}
.hero {
  position: relative;
  overflow: hidden;
  min-height: 100vh;
  padding: 0 10%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
}

/* 📌 Barras Laterales Fijas (Sidebars de Contacto) */
.sidebar-left {
  position: fixed;
  bottom: 0;
  left: 2.25rem;
  z-index: 50;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
}
.sidebar-right {
  position: fixed;
  bottom: 0;
  right: 2.25rem;
  z-index: 50;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
}
.sidebar-icon-link {
  color: #94a3b8;
  font-size: 1.35rem;
  text-decoration: none;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  transition: color 0.3s ease, transform 0.3s ease;
}
.sidebar-icon-link:hover {
  color: #22d3ee;
  transform: translateY(-4px);
}
.sidebar-email-link {
  writing-mode: vertical-rl;
  font-family: "Fira Code", "Consolas", monospace;
  font-size: 0.82rem;
  letter-spacing: 0.1em;
  color: #94a3b8;
  text-decoration: none;
  transition: color 0.3s ease, transform 0.3s ease;
}
.sidebar-email-link:hover {
  color: #22d3ee;
  transform: translateY(-4px);
}
.sidebar-line {
  width: 1px;
  height: 6rem;
  background-color: #94a3b8;
}

/* 🧭 Navbar */
.navbar a {
  color: #C1440E;
  font-weight: 500;
  font-size: 0.95rem;
  text-decoration: none;
}
.navbar a:hover {
  color: #fff;
  transition: 0.2s;
}
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
  background-color: rgba(10, 25, 47, 0.95);
  backdrop-filter: blur(8px);
  padding: 1rem 5%;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}
.logo {
  color: #C1440E;
}
.navbar-actions {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.intro-text {
  color: #C1440E;
  font-family: "Fira Code", "Consolas", monospace;
  font-size: 1rem;
  font-weight: 500;
  margin-bottom: 0.5rem;
}
.main-name {
  font-size: 3.5rem;
  font-weight: 700;
  color: #e6f1ff;
  line-height: 1.15;
  margin: 0 0 0.5rem 0;
}
.subtitle {
  font-size: 2rem;
  font-weight: 600;
  color: #cbd5e1; /* Mayor brillo y contraste sobre el fondo azul marino */
  margin: 0 0 1.25rem 0;
}
.description {
  max-width: 640px; /* Mayor ancho para lectura fluida */
  color: #b8c4e0;
  font-size: 1.05rem;
  line-height: 1.75;
}
.accent {
  color: #64ffda;
  font-weight: 500;
}
/* Botón CTA armonizado con el naranja de marca (#C1440E) */
.resume-btn {
  border: 1.5px solid #C1440E !important;
  color: #ff7a45 !important;
  background-color: rgba(193, 68, 14, 0.08) !important;
  font-weight: 600 !important;
  padding: 0.7rem 1.5rem !important;
  border-radius: 8px !important;
  transition: all 0.25s ease !important;
}
.resume-btn:hover {
  background-color: rgba(193, 68, 14, 0.22) !important;
  border-color: #ff7a45 !important;
  color: #ffffff !important;
  transform: translateY(-2px);
}

.section {
  min-height: 100vh; /* ✅ cada sección ocupa pantalla completa */
  padding: 5rem 10%;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.section-title {
  color: #64ffda;
  font-size: 1.5rem;
  font-weight: 600;
  margin-bottom: 1.5rem;
}
.section-text {
  color: #a8b2d1;
  max-width: 700px;
  margin: 0 auto;
  line-height: 1.8;
}

/* 👤 About Me - Layout de 2 columnas (imagen en color original) */
.about-container {
  display: grid;
  grid-template-columns: 3fr 2fr;
  gap: 3rem;
  align-items: center;
  max-width: 1050px;
  margin: 0 auto;
  width: 100%;
}
.about-content .section-text {
  margin: 0 0 1rem 0;
  max-width: 100%;
}
.about-content .section-text:last-child {
  margin-bottom: 0;
}
.about-image-col {
  display: flex;
  justify-content: center;
  align-items: center;
}
.profile-img-wrapper {
  position: relative;
  border-radius: 10px;
  max-width: 300px;
  width: 100%;
  overflow: hidden;
  box-shadow: 0 10px 30px -15px rgba(2, 12, 27, 0.7);
}
.profile-img {
  display: block;
  border-radius: 10px;
  width: 100%;
  height: auto;
  object-fit: cover;
}

/* 🧠 03. Skills and My Tools */
.skills-section {
  align-items: center;
}
.skills-main-title {
  font-size: 2rem;
  font-weight: 700;
  color: #ccd6f6;
  margin-bottom: 3rem;
}
.title-number {
  color: #64ffda;
  font-weight: 600;
  margin-right: 0.4rem;
}
.triangle {
  color: #64ffda;
  font-size: 0.95rem;
  margin: 0 0.4rem;
}

/* Grid de 2 columnas para Frontend y Backend */
.skills-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 4rem;
  width: 100%;
  max-width: 850px;
  margin: 0 auto 3.5rem auto;
}
.skills-column {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.skills-subtitle {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.25rem;
  font-weight: 600;
  color: #ccd6f6;
  margin-bottom: 2rem;
}
.tech-logos {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 3rem;
  flex-wrap: wrap;
}
.tech-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.65rem;
  cursor: pointer;
}
.tech-logo {
  width: 68px;
  height: 68px;
  filter: grayscale(100%);
  opacity: 0.7;
  transition: filter 0.3s ease, opacity 0.3s ease, transform 0.3s ease;
}
.tech-item:hover .tech-logo {
  filter: grayscale(0%);
  opacity: 1;
  transform: translateY(-4px);
}
.tech-name {
  font-size: 0.9rem;
  color: #8892b0;
  transition: color 0.3s ease;
}
.tech-item:hover .tech-name {
  color: #64ffda;
}

/* Tarjeta de Certificados (PrimeVue pv-card) */
.certificates-wrapper {
  width: 100%;
  max-width: 720px;
  margin: 0 auto;
}
.certificates-card {
  background-color: #112240 !important;
  border: 1px solid #233554;
  border-radius: 14px !important;
  padding: 1.75rem 2rem;
  color: #ccd6f6 !important;
  box-shadow: 0 10px 30px -15px rgba(2, 12, 27, 0.7);
}
.certificates-header {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}
.certificate-icon {
  color: #64ffda;
  font-size: 1.8rem;
}
.certificates-title {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.3rem;
  font-weight: 600;
  color: #ccd6f6;
  margin: 0;
}
.certificates-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  text-align: left;
}
.certificates-list li {
  position: relative;
  padding-left: 1.5rem;
  color: #a8b2d1;
  font-size: 0.98rem;
  line-height: 1.6;
}
.certificates-list li::before {
  content: '▸';
  position: absolute;
  left: 0;
  top: 0;
  color: #64ffda;
  font-size: 1rem;
}
.certificate-link {
  color: #a8b2d1;
  text-decoration: none;
  transition: color 0.2s ease;
}
.certificate-link:hover {
  color: #64ffda;
  text-decoration: underline;
}

.info-card {
  background-color: #112240;
  border: 1px solid #233554;
  color: #a8b2d1;
}

/* 🚀 04. My Projects (Diseño superpuesto de 2 columnas) */
.projects-section {
  background-color: #0a192f;
  align-items: center;
}
.projects-main-title {
  font-size: 2rem;
  font-weight: 700;
  color: #ccd6f6;
  margin-bottom: 3.5rem;
}
.projects-number {
  color: #64ffda;
  font-family: "Fira Code", "Consolas", monospace;
  font-weight: 600;
  margin-right: 0.5rem;
}
.featured-project-grid {
  display: grid;
  grid-template-columns: 1fr 1.15fr;
  gap: 2.5rem;
  align-items: center;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}
.project-info-col {
  position: relative;
  z-index: 2;
  text-align: left;
}
.project-overtitle {
  font-family: "Fira Code", "Consolas", monospace;
  font-size: 0.9rem;
  color: #64ffda;
  margin: 0 0 0.4rem 0;
  letter-spacing: 0.05em;
}
.project-featured-title {
  font-size: 1.9rem;
  font-weight: 700;
  color: #ccd6f6;
  margin: 0 0 1.5rem 0;
  line-height: 1.25;
}
.project-description-card {
  background-color: #112240;
  border: 1px solid #233554;
  border-radius: 8px;
  padding: 1.5rem 1.75rem;
  color: #a8b2d1;
  font-size: 0.95rem;
  line-height: 1.65;
  box-shadow: 0 20px 30px -15px rgba(2, 12, 27, 0.7);
  position: relative;
  z-index: 3;
}
.project-description-card p {
  margin: 0 0 0.85rem 0;
}
.project-description-card p:last-child {
  margin-bottom: 0;
}
.highlight-tech {
  color: #64ffda;
  font-weight: 500;
}
.project-tech-stack {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem 1.25rem;
  list-style: none;
  padding: 0;
  margin: 1.5rem 0 1.25rem 0;
  font-family: "Fira Code", "Consolas", monospace;
  font-size: 0.85rem;
  color: #a8b2d1;
}
.project-links {
  display: flex;
  align-items: center;
  gap: 1.25rem;
}
.project-icon-link {
  color: #ccd6f6;
  font-size: 1.35rem;
  text-decoration: none;
  transition: color 0.2s ease, transform 0.2s ease;
  display: inline-flex;
  align-items: center;
}
.project-icon-link:hover {
  color: #64ffda;
  transform: translateY(-2px);
}
.project-image-col {
  position: relative;
  z-index: 2;
  width: 100%;
}

/* 🖼️ Carrusel de Imágenes */
.project-carousel {
  display: flex;
  flex-direction: column;
  background-color: #112240;
  border: 1px solid #233554;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 20px 30px -15px rgba(2, 12, 27, 0.75);
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
}
.project-carousel:hover {
  border-color: rgba(100, 255, 218, 0.35);
  box-shadow: 0 25px 35px -15px rgba(2, 12, 27, 0.85);
}
.carousel-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0.85rem 1.25rem;
  background: rgba(10, 25, 47, 0.85);
  border-bottom: 1px solid #233554;
}
.carousel-header-left {
  display: flex;
  align-items: center;
  gap: 0.65rem;
}
.carousel-dot-indicator {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #64ffda;
  box-shadow: 0 0 8px #64ffda;
  display: inline-block;
  flex-shrink: 0;
}
.carousel-slide-title {
  font-family: "Fira Code", "Consolas", monospace;
  font-size: 0.95rem;
  font-weight: 600;
  color: #64ffda;
  letter-spacing: 0.03em;
}
.carousel-counter {
  font-family: "Fira Code", "Consolas", monospace;
  font-size: 0.82rem;
  color: #8892b0;
}
.carousel-viewport {
  position: relative;
  width: 100%;
  height: 350px;
  background-color: #0b192f;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
}
.carousel-slide {
  position: absolute;
  inset: 0;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.45s ease-in-out, visibility 0.45s ease-in-out;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0.5rem;
}
.carousel-slide.active {
  opacity: 1;
  visibility: visible;
  z-index: 1;
}
.carousel-img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  border-radius: 6px;
  user-select: none;
}
.carousel-arrow {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: rgba(10, 25, 47, 0.75);
  border: 1px solid #233554;
  color: #ccd6f6;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 4;
  transition: all 0.2s ease;
  backdrop-filter: blur(4px);
  opacity: 0;
}
.carousel-viewport:hover .carousel-arrow {
  opacity: 1;
}
.carousel-arrow:hover {
  background: #112240;
  color: #64ffda;
  border-color: #64ffda;
  transform: translateY(-50%) scale(1.08);
}
.carousel-arrow.prev {
  left: 10px;
}
.carousel-arrow.next {
  right: 10px;
}
.carousel-indicators {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.65rem;
  padding: 0.85rem 1rem;
  background: rgba(10, 25, 47, 0.6);
  border-top: 1px solid #233554;
}
.carousel-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: transparent;
  border: 1.5px solid #64ffda;
  cursor: pointer;
  padding: 0;
  transition: all 0.3s ease;
}
.carousel-dot:hover {
  background-color: rgba(100, 255, 218, 0.4);
  transform: scale(1.2);
}
.carousel-dot.active {
  background-color: #64ffda;
  width: 24px;
  border-radius: 6px;
  box-shadow: 0 0 10px rgba(100, 255, 218, 0.6);
}

@media (max-width: 960px) {
  .featured-project-grid {
    grid-template-columns: 1fr;
    gap: 2.5rem;
  }
}

/* ⚙️ Footer */
.footer {
  border-top: 1px solid #233554;
  color: #8892b0;
}

/* mobile menu toggle (hamburger) */
.menu-toggle {
  /* eliminamos los !important y estilos inline para mantener control desde CSS */
  display: none; /* oculto por defecto, solo mostrar en mobile via media query */
  align-items: center;
  justify-content: center;
  gap: 6px;
  width: 44px;
  height: 40px;
  padding: 6px;
  background: rgba(255,255,255,0.04);
  border: 1px solid rgba(255,255,255,0.06);
  border-radius: 8px;
  cursor: pointer;
  z-index: 1100;
  transition: transform 0.18s ease, background 0.15s ease;
  color: var(--color-text); /* hace que el svg herede color */
}

/* SVG hamburger - usar stroke para mejor control de color */
.menu-toggle .hamburger-icon {
  width: 22px;
  height: 18px;
  display: block;
}
.menu-toggle .line {
  fill: none;
  stroke: currentColor; /* usa color del padre */
  stroke-width: 2;
  stroke-linecap: round;
  transition: transform 0.18s ease, opacity 0.12s ease;
  transform-box: fill-box; /* asegurar que el origen sea relativo al rect */
  transform-origin: center;
}
.menu-toggle[aria-expanded="true"] .line1 {
  transform: translateY(3px) rotate(45deg);
}
.menu-toggle[aria-expanded="true"] .line2 {
  opacity: 0;
}
.menu-toggle[aria-expanded="true"] .line3 {
  transform: translateY(-3px) rotate(-45deg);
}

@media (max-width: 767px) {
  .sidebar-left,
  .sidebar-right {
    display: none;
  }

  .main-name {
    font-size: 2.5rem;
  }

  .subtitle {
    font-size: 1.45rem;
  }

  .about-container {
    grid-template-columns: 1fr;
    gap: 2.5rem;
  }

  .skills-grid {
    grid-template-columns: 1fr;
    gap: 2.5rem;
  }

  .certificates-card {
    padding: 1.25rem 1rem;
  }

  .featured-project-grid {
    display: flex;
    flex-direction: column;
    gap: 2rem;
  }
  .project-info-col,
  .project-image-col {
    width: 100%;
  }
  .carousel-viewport {
    height: 240px;
  }
  .carousel-arrow {
    opacity: 0.85;
  }

  /* show hamburger */
  .menu-toggle {
    display: inline-flex; /* visible en móvil */
    position: relative; /* se muestra dentro del header */
    margin-left: auto; /* empuja el toggle a la derecha en el header */
    background: rgba(255,255,255,0.06);
    box-shadow: 0 6px 18px rgba(0,0,0,0.12);
    border: 1px solid rgba(255,255,255,0.08);
  }

  /* hide desktop nav inline, turn into panel */
  .nav-menu {
    position: fixed;
    top: 64px; /* below header */
    left: 0;
    right: 0;
    display: none !important; /* FORZAR oculto en móvil */
    flex-direction: column;
    gap: 12px;
    padding: 16px 5%;
    background-color: rgba(10, 25, 47, 0.98);
    z-index: 1050;
    border-bottom: 1px solid rgba(255,255,255,0.04);
  }

  .nav-menu.open {
    display: flex !important; /* FORZAR visible cuando abierto */
  }

  .nav-menu a {
    font-size: 1.05rem;
    padding: 8px 0;
  }

  /* adjust logo spacing when menu visible */
  .logo {
    z-index: 1101;
  }
}

/* keep existing desktop look intact */
@media (min-width: 768px) {
  .nav-menu {
    display: flex !important;
    position: static;
  }
}
</style>
