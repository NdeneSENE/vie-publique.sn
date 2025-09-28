<template>
  <div>
    <!-- Navigation bottom moderne avec glassmorphism -->
    <nav 
      class="fixed bottom-0 left-0 right-0 z-50 md:hidden transform transition-all duration-300 ease-in-out"
      :class="isVisible ? 'translate-y-0' : 'translate-y-full'"
    >
      <!-- Fond avec glassmorphism -->
      <div class="absolute inset-0 bg-white/95 dark:bg-gray-900/95 backdrop-blur-lg border-t border-gray-200/80 dark:border-gray-700/80" />
      
      <!-- Shadow élégante -->
      <div class="absolute inset-0 shadow-lg shadow-black/5 dark:shadow-black/20" />
      
      <!-- Safe area pour iPhone -->
      <div class="relative pb-safe">
        <div class="flex items-center justify-around px-2 py-2">
          <NuxtLink
            v-for="(tab, index) in tabs"
            :key="tab.name"
            :to="tab.to"
            class="nav-item group relative flex flex-col items-center justify-center min-w-0 flex-1 px-1 py-2 transition-all duration-200 ease-out"
            :class="getTabClasses(tab)"
          >
            <!-- Indicateur actif animé -->
            <div 
              class="absolute -top-0.5 left-1/2 h-1 bg-gradient-to-r from-blue-500 to-purple-600 rounded-full transition-all duration-300 ease-out transform -translate-x-1/2"
              :class="isActiveTab(tab) ? 'w-8 opacity-100' : 'w-0 opacity-0'"
            />
            
            <!-- Container de l'icône avec effet de bounce -->
            <div 
              class="relative flex h-7 w-7 items-center justify-center rounded-xl transition-all duration-200 ease-out"
              :class="getIconContainerClasses(tab)"
            >
              <!-- Background animé pour l'état actif -->
              <div 
                class="absolute inset-0 rounded-xl bg-gradient-to-br from-blue-500/20 to-purple-600/20 dark:from-blue-400/20 dark:to-purple-500/20 transition-all duration-300 ease-out"
                :class="isActiveTab(tab) ? 'scale-100 opacity-100' : 'scale-75 opacity-0'"
              />
              
              <!-- Icône -->
              <UIcon 
                :name="tab.icon" 
                class="relative z-10 h-5 w-5 transition-all duration-200 ease-out"
                :class="getIconClasses(tab)"
              />
              
              <!-- Badge de notification (optionnel) -->
              <div 
                v-if="tab.badge"
                class="absolute -top-1 -right-1 flex h-4 w-4 items-center justify-center rounded-full bg-red-500 text-[10px] font-bold text-white ring-2 ring-white dark:ring-gray-900 transition-all duration-200"
                :class="isActiveTab(tab) ? 'scale-110' : 'scale-100'"
              >
                {{ tab.badge }}
              </div>
            </div>
            
            <!-- Label avec animation de couleur -->
            <span 
              class="mt-1 text-[11px] font-medium leading-none transition-all duration-200 ease-out"
              :class="getLabelClasses(tab)"
            >
              {{ tab.label }}
            </span>
            
            <!-- Effet de ripple au tap (mobile) -->
            <div 
              class="absolute inset-0 rounded-xl bg-black/5 dark:bg-white/5 scale-0 transition-transform duration-150 ease-out group-active:scale-100"
            />
          </NuxtLink>
        </div>
      </div>
    </nav>
    
    <!-- Spacer pour éviter que le contenu soit caché -->
    <div class="h-20 md:hidden" />
  </div>
</template>

<script setup>
const route = useRoute();
const activeTab = ref("");
const isVisible = ref(true);
const lastScrollY = ref(0);

const tabs = [
  { 
    label: "Accueil", 
    name: "accueil", 
    icon: "i-heroicons-home", 
    to: "/" 
  },
  {
    label: "Actualités",
    name: "actualites",
    icon: "i-heroicons-newspaper",
    to: "/actualites",
    badge: "3", // Exemple de badge
  },
  {
    label: "Documents",
    name: "documents",
    icon: "i-heroicons-rectangle-stack",
    to: "/documents",
  },
  {
    label: "Assemblée",
    name: "assemblee",
    icon: "i-heroicons-building-library",
    to: "/assemblee-nationale",
  },
  {
    label: "Menu",
    name: "voirplus",
    icon: "i-heroicons-squares-plus",
    to: "/menu",
  },
];

// Fonction pour déterminer si un onglet est actif
const isActiveTab = (tab) => {
  if (tab.to === "/") {
    return route.path === "/";
  }
  return route.path.startsWith(tab.to);
};

// Classes pour l'état de l'onglet
const getTabClasses = (tab) => {
  return isActiveTab(tab) 
    ? 'text-blue-600 dark:text-blue-400' 
    : 'text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-300';
};

// Classes pour le container de l'icône
const getIconContainerClasses = (tab) => {
  return isActiveTab(tab) 
    ? 'transform scale-110' 
    : 'group-hover:scale-105 group-active:scale-95';
};

// Classes pour l'icône
const getIconClasses = (tab) => {
  return isActiveTab(tab) 
    ? 'text-blue-600 dark:text-blue-400 drop-shadow-sm' 
    : 'text-gray-500 dark:text-gray-400 group-hover:text-gray-700 dark:group-hover:text-gray-300';
};

// Classes pour le label
const getLabelClasses = (tab) => {
  return isActiveTab(tab) 
    ? 'text-blue-600 dark:text-blue-400 font-semibold' 
    : 'text-gray-500 dark:text-gray-400 group-hover:text-gray-700 dark:group-hover:text-gray-300';
};

// Auto-hide sur scroll (optionnel)
const handleScroll = () => {
  const currentScrollY = window.scrollY;
  
  // Masquer si on scroll vers le bas, afficher si on scroll vers le haut
  if (currentScrollY > lastScrollY.value && currentScrollY > 100) {
    isVisible.value = false;
  } else {
    isVisible.value = true;
  }
  
  lastScrollY.value = currentScrollY;
};

// Watchers et lifecycle
watch(
  () => route.path,
  (newPath) => {
    const matchTab = tabs.find((tab) => {
      if (tab.to === "/") {
        return newPath === "/";
      }
      return newPath.startsWith(tab.to);
    });
    activeTab.value = matchTab ? matchTab.name : "";
  },
  { immediate: true }
);

onMounted(() => {
  // Optionnel : activer l'auto-hide sur scroll
  // window.addEventListener('scroll', handleScroll, { passive: true });
});

onBeforeUnmount(() => {
  // window.removeEventListener('scroll', handleScroll);
});
</script>

<style scoped>
/* Support pour les safe areas iOS */
.pb-safe {
  padding-bottom: env(safe-area-inset-bottom, 0.5rem);
}

/* Animation de bounce subtile pour les icônes actives */
@keyframes subtle-bounce {
  0%, 100% {
    transform: scale(1.1);
  }
  50% {
    transform: scale(1.15);
  }
}

.nav-item.active .icon-container {
  animation: subtle-bounce 0.6s ease-in-out;
}

/* Effet de press pour mobile */
.nav-item:active {
  transform: scale(0.95);
  transition: transform 0.1s ease-out;
}

/* Amélioration de la performance avec will-change */
.nav-item {
  will-change: transform;
}

/* Glassmorphism amélioré pour les appareils qui le supportent */
@supports (backdrop-filter: blur(20px)) {
  .backdrop-blur-lg {
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
  }
}

/* Fallback pour les anciens navigateurs */
@supports not (backdrop-filter: blur(20px)) {
  nav > div:first-child {
    background: rgba(255, 255, 255, 0.98);
  }
  
  .dark nav > div:first-child {
    background: rgba(17, 24, 39, 0.98);
  }
}

/* Micro-interactions améliorées */
.nav-item:hover .icon-container {
  transform: translateY(-1px) scale(1.05);
}

.nav-item:active .icon-container {
  transform: translateY(0) scale(0.95);
}

/* Responsive pour les très petits écrans */
@media (max-width: 360px) {
  .nav-item {
    padding-left: 0.25rem;
    padding-right: 0.25rem;
  }
  
  .nav-item span {
    font-size: 10px;
  }
}

/* Optimisation pour les écrans à haute densité */
@media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi) {
  .border-t {
    border-width: 0.5px;
  }
}

/* Animation de l'indicateur actif */
@keyframes indicator-slide {
  from {
    width: 0;
    opacity: 0;
  }
  to {
    width: 2rem;
    opacity: 1;
  }
}

/* États focus pour l'accessibilité */
.nav-item:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 0.75rem;
}

/* Transition pour le mode sombre */
@media (prefers-color-scheme: dark) {
  .nav-item:focus-visible {
    outline-color: #60a5fa;
  }
}

/* Amélioration de la lisibilité */
.nav-item span {
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.dark .nav-item span {
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}
</style>