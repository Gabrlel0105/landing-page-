<script setup>
import { useI18n } from "vue-i18n";
import { ref, watch, onMounted, onBeforeUnmount } from "vue";

import Select from 'primevue/select';

const { locale } = useI18n();

const languages = ref([
  { code: "es", name: "Español" },
  { code: "en", name: "English" }
]);

const selected = ref(languages.value.find(l => l.code === locale.value));

watch(selected, (newVal) => {
  if (newVal) {
    locale.value = newVal.code;
    // close mobile menu after selecting
    open.value = false;
  }
});

// responsive hamburger menu state
const open = ref(false);
const root = ref(null);

function toggle() {
  open.value = !open.value;
}

function handleDocClick(e) {
  if (!root.value) return;
  if (!root.value.contains(e.target)) {
    open.value = false;
  }
}

onMounted(() => {
  document.addEventListener('click', handleDocClick);
});

onBeforeUnmount(() => {
  document.removeEventListener('click', handleDocClick);
});
</script>

<template>
  <div class="lang-switcher" ref="root">
    <button
      class="hamburger"
      :aria-expanded="open.toString()"
      aria-label="Toggle language menu"
      @click.stop.prevent="toggle"
    >
      <span class="bar"></span>
      <span class="bar"></span>
      <span class="bar"></span>
    </button>

    <div class="lang-panel" :class="{ open: open }">
      <Select
        v-model="selected"
        :options="languages"
        optionLabel="name"
        class="lang-select"
      >
        <template #option="slotProps">
          <div class="lang-option">
            <span class="flag">{{ slotProps.option.flag }}</span>
            <span>{{ slotProps.option.name }}</span>
          </div>
        </template>

        <template #value="slotProps">
          <div class="lang-option" v-if="slotProps.value">
            <span class="flag">{{ slotProps.value.flag }}</span>
            <span>{{ slotProps.value.name }}</span>
          </div>
          <span v-else></span>
        </template>
      </Select>
    </div>
  </div>
</template>

<style scoped>
.lang-switcher {
  display: flex;
  align-items: center;
  gap: 8px;
  position: relative;
}

.hamburger {
  display: inline-flex;
  flex-direction: column;
  justify-content: center;
  gap: 4px;
  width: 44px;
  height: 40px;
  padding: 8px; /* área táctil mayor */
  background: var(--color-background-soft);
  border: 1px solid var(--color-border);
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s, transform 0.15s, box-shadow 0.15s;
  z-index: 120; /* asegurar encima */
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}

.hamburger:active {
  transform: scale(0.98);
}

.hamburger .bar {
  display: block;
  height: 2px;
  width: 100%;
  background: var(--color-text);
  border-radius: 2px;
  transition: transform 0.15s ease, opacity 0.12s ease;
  transform-origin: center;
  transform-box: fill-box;
}

/* visible X state when open */
.hamburger[aria-expanded="true"] .bar:nth-child(1) {
  transform: translateY(4px) rotate(45deg);
}
.hamburger[aria-expanded="true"] .bar:nth-child(2) {
  opacity: 0;
}
.hamburger[aria-expanded="true"] .bar:nth-child(3) {
  transform: translateY(-4px) rotate(-45deg);
}

/* panel that holds the select */
.lang-panel {
  display: none; /* mobile: hidden until opened */
  position: absolute;
  right: 0;
  top: calc(100% + 8px);
  z-index: 40;
  min-width: 160px;
  padding: 8px;
  border-radius: 8px;
  box-shadow: 0 6px 18px rgba(0,0,0,0.08);
  background: var(--color-background);
}

.lang-panel.open {
  display: block;
}

/* Ensure the select looks like the page background and inherits text color */
.lang-select {
  min-width: 140px;
  max-width: 100%;
  font-size: 0.95rem;
  background-color: #112240 !important;
  color: #ccd6f6 !important;
  border: 1px solid #233554 !important;
  border-radius: 8px !important;
}

:deep(.p-select-label),
:deep(.p-select-dropdown) {
  color: #ccd6f6 !important;
}

/* Option styling inside dropdown */
.lang-option {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #ccd6f6 !important;
  font-weight: 500;
}

.flag {
  font-size: 1.2rem;
}

/* Desktop: always show panel inline and hide hamburger */
@media (min-width: 600px) {
  .hamburger {
    display: none;
  }

  .lang-panel {
    display: block !important;
    position: static;
    padding: 0;
    box-shadow: none;
    background: transparent;
    min-width: 140px;
  }

  /* ensure dropdown fits nicely inline */
  ::v-deep .lang-panel .p-dropdown {
    min-width: 140px;
    background: transparent !important;
    border: none !important;
  }
}

/* Small tweak for very small screens */
@media (max-width: 599px) {
  .hamburger {
    display: inline-flex; /* asegurar visible en móvil */
  }

  .lang-panel {
    right: 6px;
    left: 6px;
    min-width: unset;
  }
}

@media (max-width: 320px) {
  .hamburger {
    width: 36px;
    height: 32px;
  }

  .lang-panel {
    right: 6px;
    left: 6px;
    min-width: unset;
  }
}
</style>
