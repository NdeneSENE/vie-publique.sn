<script setup lang="ts">
interface NavigationCard {
  title: string;
  description: string;
  icon: string;
  to: string;
  badge?: string;
  isNew?: boolean;
}

interface CardConfig {
  gradient: string;
  hoverGradient: string;
  iconColor: string;
  bgColor: string;
}

interface CardConfigs {
  [key: string]: CardConfig;
}

const props = defineProps<{
  navigationCards: NavigationCard[];
}>();

// État de la recherche
const searchQuery = ref('');
const searchResults = ref<NavigationCard[]>([]);
const isSearchFocused = ref(false);
const searchInput = ref<HTMLInputElement | null>(null);

// Suggestions de recherche populaires
const popularSearches = [
  { text: "Assemblée Nationale", icon: "i-heroicons-building-library" },
  { text: "Journal officiel", icon: "i-heroicons-newspaper" },
  { text: "Budget 2025", icon: "i-heroicons-banknotes" },
  { text: "Conseil des ministres", icon: "i-heroicons-document-text" },
  { text: "Documents officiels", icon: "i-heroicons-rectangle-stack" },
];

// Recherche en temps réel
const performSearch = () => {
  if (!searchQuery.value.trim()) {
    searchResults.value = [];
    return;
  }

  const query = searchQuery.value.toLowerCase();
  searchResults.value = props.navigationCards.filter(card => 
    card.title.toLowerCase().includes(query) ||
    card.description.toLowerCase().includes(query)
  );
};

// Gestion du focus
const handleSearchFocus = () => {
  isSearchFocused.value = true;
};

const handleSearchBlur = () => {
  // Délai pour permettre les clics sur les suggestions
  setTimeout(() => {
    isSearchFocused.value = false;
  }, 200);
};

// Navigation au clavier
const handleKeydown = (event: KeyboardEvent) => {
  if (event.key === 'Escape') {
    searchQuery.value = '';
    searchResults.value = [];
    searchInput.value?.blur();
  }
};

// Sélection d'une suggestion
const selectSuggestion = (suggestion: string) => {
  searchQuery.value = suggestion;
  performSearch();
  searchInput.value?.focus();
};

// Watcher pour la recherche en temps réel
watch(searchQuery, performSearch);

// Raccourci clavier pour le focus
onMounted(() => {
  const handleGlobalKeydown = (event: KeyboardEvent) => {
    if ((event.metaKey || event.ctrlKey) && event.key === 'k') {
      event.preventDefault();
      searchInput.value?.focus();
    }
  };
  
  document.addEventListener('keydown', handleGlobalKeydown);
  
  onBeforeUnmount(() => {
    document.removeEventListener('keydown', handleGlobalKeydown);
  });
});

// Configuration des thèmes par carte avec gradients modernes
const cardConfigs: CardConfigs = {
  "Assemblée Nationale": {
    gradient: "from-blue-50 to-indigo-50 dark:from-blue-950/30 dark:to-indigo-950/30",
    hoverGradient: "group-hover:from-blue-100 group-hover:to-indigo-100 dark:group-hover:from-blue-900/40 dark:group-hover:to-indigo-900/40",
    iconColor: "text-blue-600 dark:text-blue-400",
    bgColor: "bg-blue-500/10 dark:bg-blue-400/10"
  },
  "Journal officiel Sénégal": {
    gradient: "from-red-50 to-rose-50 dark:from-red-950/30 dark:to-rose-950/30",
    hoverGradient: "group-hover:from-red-100 group-hover:to-rose-100 dark:group-hover:from-red-900/40 dark:group-hover:to-rose-900/40",
    iconColor: "text-red-600 dark:text-red-400",
    bgColor: "bg-red-500/10 dark:bg-red-400/10"
  },
  "Budget du Sénégal": {
    gradient: "from-emerald-50 to-teal-50 dark:from-emerald-950/30 dark:to-teal-950/30",
    hoverGradient: "group-hover:from-emerald-100 group-hover:to-teal-100 dark:group-hover:from-emerald-900/40 dark:group-hover:to-teal-900/40",
    iconColor: "text-emerald-600 dark:text-emerald-400",
    bgColor: "bg-emerald-500/10 dark:bg-emerald-400/10"
  },
  "Conseil des ministres": {
    gradient: "from-amber-50 to-orange-50 dark:from-amber-950/30 dark:to-orange-950/30",
    hoverGradient: "group-hover:from-amber-100 group-hover:to-orange-100 dark:group-hover:from-amber-900/40 dark:group-hover:to-orange-900/40",
    iconColor: "text-amber-600 dark:text-amber-400",
    bgColor: "bg-amber-500/10 dark:bg-amber-400/10"
  },
  "Annuaire": {
    gradient: "from-violet-50 to-purple-50 dark:from-violet-950/30 dark:to-purple-950/30",
    hoverGradient: "group-hover:from-violet-100 group-hover:to-purple-100 dark:group-hover:from-violet-900/40 dark:group-hover:to-purple-900/40",
    iconColor: "text-violet-600 dark:text-violet-400",
    bgColor: "bg-violet-500/10 dark:bg-violet-400/10"
  },
  "Documents": {
    gradient: "from-indigo-50 to-blue-50 dark:from-indigo-950/30 dark:to-blue-950/30",
    hoverGradient: "group-hover:from-indigo-100 group-hover:to-blue-100 dark:group-hover:from-indigo-900/40 dark:group-hover:to-blue-900/40",
    iconColor: "text-indigo-600 dark:text-indigo-400",
    bgColor: "bg-indigo-500/10 dark:bg-indigo-400/10"
  },
} as const;

// Configuration par défaut pour les cartes non configurées
const defaultConfig: CardConfig = {
  gradient: "from-gray-50 to-slate-50 dark:from-gray-950/30 dark:to-slate-950/30",
  hoverGradient: "group-hover:from-gray-100 group-hover:to-slate-100 dark:group-hover:from-gray-900/40 dark:group-hover:to-slate-900/40",
  iconColor: "text-gray-600 dark:text-gray-400",
  bgColor: "bg-gray-500/10 dark:bg-gray-400/10"
};

// Fonction pour obtenir la configuration d'une carte
const getCardConfig = (title: string): CardConfig => {
  return cardConfigs[title] || defaultConfig;
};
</script>

<template>
  <div class="py-8 sm:py-12">
    <!-- Header de section moderne -->
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <div class="text-center">
        <h2 class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white">
          Explorez nos données
        </h2>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4">
          Accédez facilement à toutes les informations officielles
        </p>
      </div>

      <!-- Barre de recherche moderne style Google/Pappers -->
      <div class="mt-8 sm:mt-10">
        <div class="mx-auto max-w-2xl">
          <!-- Container de recherche avec effet de focus -->
          <div
            class="search-container relative z-[100] transition-all duration-300"
            :class="isSearchFocused ? 'search-focused' : ''"
          >
            <!-- Input principal -->
            <div class="relative">
              <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-4">
                <UIcon 
                  name="i-heroicons-magnifying-glass" 
                  class="h-5 w-5 text-gray-400 transition-colors duration-200"
                  :class="isSearchFocused ? 'text-blue-500 dark:text-blue-400' : ''"
                />
              </div>
              
              <input
                ref="searchInput"
                v-model="searchQuery"
                type="search"
                placeholder="Rechercher des documents, services, informations..."
                class="search-input w-full rounded-2xl border-0 bg-white py-4 pl-12 pr-20 text-gray-900 shadow-lg ring-1 ring-gray-200 placeholder:text-gray-400 focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 sm:text-sm dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:placeholder:text-gray-500 dark:focus:ring-blue-400"
                @focus="handleSearchFocus"
                @blur="handleSearchBlur"
                @keydown="handleKeydown"
                autocomplete="off"
                spellcheck="false"
              />
              
              <!-- Raccourci clavier et bouton clear -->
              <div class="absolute inset-y-0 right-0 flex items-center pr-4">
                <div class="hidden items-center space-x-1 sm:flex">
                  <kbd class="inline-flex items-center rounded border border-gray-200 px-2 py-1 font-sans text-xs text-gray-400 dark:border-gray-700 dark:text-gray-500">
                    ⌘K
                  </kbd>
                </div>
                
                <!-- Bouton clear -->
                <button
                  v-if="searchQuery"
                  @click="searchQuery = ''; searchResults = []"
                  class="ml-2 flex h-6 w-6 items-center justify-center rounded-full bg-gray-100 text-gray-400 hover:bg-gray-200 hover:text-gray-600 dark:bg-gray-700 dark:hover:bg-gray-600 dark:hover:text-gray-300"
                >
                  <UIcon name="i-heroicons-x-mark" class="h-3 w-3" />
                </button>
              </div>
            </div>

            <!-- Dropdown de suggestions/résultats -->
            <div
              v-if="isSearchFocused || searchQuery"
              class="search-dropdown absolute left-0 right-0 top-full z-[9999] mt-2 max-h-96 overflow-y-auto rounded-2xl bg-white shadow-2xl ring-1 ring-black/5 dark:bg-gray-800 dark:ring-white/10"
              style="position: absolute; z-index: 9999;"
            >
              <!-- Résultats de recherche -->
              <div v-if="searchQuery && searchResults.length > 0" class="p-2">
                <div class="mb-2 px-3 py-2 text-xs font-semibold uppercase tracking-wide text-gray-500 dark:text-gray-400">
                  Résultats ({{ searchResults.length }})
                </div>
                <NuxtLink
                  v-for="result in searchResults"
                  :key="result.title"
                  :to="result.to"
                  class="flex items-center space-x-3 rounded-xl px-3 py-3 transition-colors duration-150 hover:bg-gray-50 dark:hover:bg-gray-700"
                  @click="isSearchFocused = false"
                >
                  <div 
                    class="flex h-10 w-10 flex-shrink-0 items-center justify-center rounded-lg"
                    :class="getCardConfig(result.title).bgColor"
                  >
                    <UIcon
                      :name="result.icon"
                      class="h-5 w-5"
                      :class="getCardConfig(result.title).iconColor"
                    />
                  </div>
                  <div class="min-w-0 flex-1">
                    <div class="text-sm font-medium text-gray-900 dark:text-white">
                      {{ result.title }}
                    </div>
                    <div class="text-sm text-gray-500 dark:text-gray-400 truncate">
                      {{ result.description }}
                    </div>
                  </div>
                  <UIcon name="i-heroicons-arrow-up-right" class="h-4 w-4 text-gray-400" />
                </NuxtLink>
              </div>

              <!-- Aucun résultat -->
              <div v-else-if="searchQuery && searchResults.length === 0" class="p-6 text-center">
                <UIcon name="i-heroicons-magnifying-glass" class="mx-auto h-12 w-12 text-gray-400" />
                <h3 class="mt-2 text-sm font-medium text-gray-900 dark:text-white">
                  Aucun résultat trouvé
                </h3>
                <p class="mt-1 text-sm text-gray-500 dark:text-gray-400">
                  Essayez avec d'autres mots-clés
                </p>
              </div>

              <!-- Suggestions populaires -->
              <div v-else class="p-2">
                <div class="mb-2 px-3 py-2 text-xs font-semibold uppercase tracking-wide text-gray-500 dark:text-gray-400">
                  Recherches populaires
                </div>
                <button
                  v-for="suggestion in popularSearches"
                  :key="suggestion.text"
                  @click="selectSuggestion(suggestion.text)"
                  class="flex w-full items-center space-x-3 rounded-xl px-3 py-2 text-left transition-colors duration-150 hover:bg-gray-50 dark:hover:bg-gray-700"
                >
                  <UIcon 
                    :name="suggestion.icon" 
                    class="h-4 w-4 text-gray-400"
                  />
                  <span class="text-sm text-gray-700 dark:text-gray-300">
                    {{ suggestion.text }}
                  </span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Grille des cartes avec animation séquentielle -->
      <div class="mt-8 sm:mt-12">
        <div
          class="grid grid-cols-1 gap-4 sm:grid-cols-2 sm:gap-6 lg:grid-cols-3 xl:gap-8"
        >
          <NuxtLink
            v-for="(card, index) in navigationCards"
            :key="card.title"
            :to="card.to"
            class="group relative block"
            :style="{ animationDelay: `${index * 100}ms` }"
          >
            <!-- Carte principale -->
            <div
              class="card-modern relative overflow-hidden rounded-2xl border border-gray-200/60 bg-gradient-to-br transition-all duration-300 ease-out dark:border-gray-700/60"
              :class="[
                getCardConfig(card.title).gradient,
                getCardConfig(card.title).hoverGradient
              ]"
            >
              <!-- Effet de brillance au hover -->
              <div class="absolute inset-0 bg-gradient-to-r from-transparent via-white/10 to-transparent opacity-0 transition-opacity duration-500 group-hover:opacity-100 dark:via-white/5" />
              
              <!-- Badge "Nouveau" si applicable -->
              <div 
                v-if="card.isNew"
                class="absolute right-3 top-3 z-10"
              >
                <span class="inline-flex items-center rounded-full bg-blue-600 px-2 py-1 text-xs font-medium text-white shadow-sm dark:bg-blue-500">
                  Nouveau
                </span>
              </div>

              <!-- Badge personnalisé -->
              <div 
                v-if="card.badge"
                class="absolute right-3 top-3 z-10"
              >
                <span class="inline-flex items-center rounded-full bg-red-600 px-2 py-1 text-xs font-medium text-white shadow-sm dark:bg-red-500">
                  {{ card.badge }}
                </span>
              </div>

              <!-- Contenu de la carte -->
              <div class="relative p-6">
                <!-- Container de l'icône avec effet de background -->
                <div class="flex items-start space-x-4">
                  <div 
                    class="flex h-12 w-12 flex-shrink-0 items-center justify-center rounded-xl bg-gradient-to-br transition-all duration-300 group-hover:scale-110 group-hover:rotate-3"
                    :class="getCardConfig(card.title).bgColor"
                  >
                    <UIcon
                      :name="card.icon"
                      class="h-6 w-6 transition-all duration-300"
                      :class="getCardConfig(card.title).iconColor"
                    />
                  </div>

                  <!-- Texte -->
                  <div class="min-w-0 flex-1">
                    <h3 class="text-lg font-semibold text-gray-900 transition-colors duration-200 group-hover:text-gray-700 dark:text-white dark:group-hover:text-gray-200">
                      {{ card.title }}
                    </h3>
                    <p 
                      v-if="card.description"
                      class="mt-2 text-sm text-gray-600 transition-colors duration-200 group-hover:text-gray-500 dark:text-gray-400 dark:group-hover:text-gray-300"
                    >
                      {{ card.description }}
                    </p>
                  </div>
                </div>

                <!-- Flèche indicatrice -->
                <div class="mt-4 flex items-center justify-between">
                  <div class="flex-1" />
                  <div class="flex h-8 w-8 items-center justify-center rounded-full bg-white/50 transition-all duration-300 group-hover:bg-white/80 group-hover:scale-110 dark:bg-gray-900/50 dark:group-hover:bg-gray-900/80">
                    <UIcon
                      name="i-heroicons-arrow-right"
                      class="h-4 w-4 text-gray-600 transition-transform duration-300 group-hover:translate-x-0.5 dark:text-gray-400"
                    />
                  </div>
                </div>
              </div>

              <!-- Effet de border animé -->
              <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-gray-900/10 dark:ring-white/10 dark:group-hover:ring-white/20" />
            </div>

            <!-- Shadow dynamique -->
            <div class="absolute inset-0 -z-10 rounded-2xl bg-gray-900/5 opacity-0 transition-all duration-300 group-hover:opacity-100 dark:bg-white/5" 
                 style="transform: translate(4px, 4px)" />
          </NuxtLink>
        </div>
      </div>

      <!-- Call to action optionnel -->
      <div class="mt-12 text-center">
        <NuxtLink
          to="/menu"
          class="inline-flex items-center rounded-full bg-white px-6 py-3 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 transition-all duration-200 hover:bg-gray-50 hover:shadow-md dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:hover:bg-gray-700"
        >
          Voir tous les services
          <UIcon name="i-heroicons-chevron-right" class="ml-2 h-4 w-4" />
        </NuxtLink>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Styles pour la barre de recherche */
.search-container {
  perspective: 1000px;
  position: relative;
  z-index: 10;
}

.search-input {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

.search-input:focus {
  transform: translateY(-2px);
  box-shadow: 
    0 20px 25px -5px rgba(0, 0, 0, 0.1),
    0 10px 10px -5px rgba(0, 0, 0, 0.04),
    0 0 0 1px rgb(59 130 246 / 0.3);
}

.search-focused .search-input {
  box-shadow: 
    0 25px 50px -12px rgba(0, 0, 0, 0.15),
    0 0 0 1px rgb(59 130 246 / 0.4);
}

.dark .search-input:focus,
.dark .search-focused .search-input {
  box-shadow: 
    0 20px 25px -5px rgba(0, 0, 0, 0.4),
    0 10px 10px -5px rgba(0, 0, 0, 0.2),
    0 0 0 1px rgb(96 165 250 / 0.4);
}

/* Animation du dropdown */
.search-dropdown {
  animation: searchDropdownFadeIn 0.2s ease-out;
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  position: absolute !important;
  z-index: 9999 !important;
}

@keyframes searchDropdownFadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

/* Effet de hover pour les suggestions */
.search-dropdown button:hover,
.search-dropdown a:hover {
  transform: translateX(4px);
}

/* Amélioration de la scrollbar pour le dropdown */
.search-dropdown::-webkit-scrollbar {
  width: 6px;
}

.search-dropdown::-webkit-scrollbar-track {
  background: transparent;
}

.search-dropdown::-webkit-scrollbar-thumb {
  background: rgba(156, 163, 175, 0.4);
  border-radius: 3px;
}

.search-dropdown::-webkit-scrollbar-thumb:hover {
  background: rgba(156, 163, 175, 0.6);
}

/* Style pour les kbd (raccourcis clavier) */
kbd {
  box-shadow: 
    0 1px 1px rgba(0, 0, 0, 0.1),
    0 2px 2px rgba(0, 0, 0, 0.1);
}

/* Responsive pour mobile */
@media (max-width: 640px) {
  .search-input {
    padding-left: 2.5rem;
    padding-right: 2.5rem;
    font-size: 16px; /* Évite le zoom sur iOS */
  }
  
  .search-dropdown {
    margin-left: -1rem;
    margin-right: -1rem;
    border-radius: 1rem;
  }
}

/* Animation d'apparition séquentielle */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.card-modern {
  animation: fadeInUp 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift au hover */
.group:hover .card-modern {
  transform: translateY(-4px);
  box-shadow: 
    0 20px 25px -5px rgba(0, 0, 0, 0.1),
    0 10px 10px -5px rgba(0, 0, 0, 0.04);
}

.dark .group:hover .card-modern {
  box-shadow: 
    0 20px 25px -5px rgba(0, 0, 0, 0.3),
    0 10px 10px -5px rgba(0, 0, 0, 0.2);
}

/* Effet de brillance animé */
@keyframes shine {
  0% {
    transform: translateX(-100%) skewX(-15deg);
  }
  100% {
    transform: translateX(200%) skewX(-15deg);
  }
}

.group:hover .card-modern::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
  animation: shine 0.8s ease-out;
  pointer-events: none;
}

/* Micro-interactions pour les badges */
.group:hover [class*="bg-blue-"]:not(.bg-gradient-to-br),
.group:hover [class*="bg-red-"] {
  transform: scale(1.05);
  transition: transform 0.2s ease-out;
}

/* États focus pour l'accessibilité */
.group:focus-visible .card-modern {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
}

/* Optimisations performance */
.card-modern {
  will-change: transform, box-shadow;
}

.group:hover .card-modern {
  will-change: auto;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .card-modern {
    padding: 1rem;
  }
  
  .card-modern h3 {
    font-size: 1rem;
    line-height: 1.4;
  }
}

/* Amélioration de la lisibilité */
@media (prefers-contrast: high) {
  .card-modern {
    border-width: 2px;
  }
  
  .text-gray-600 {
    color: rgb(55, 65, 81);
  }
  
  .dark .text-gray-400 {
    color: rgb(156, 163, 175);
  }
}

/* Réduction d'animation pour l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .card-modern {
    animation: none;
    opacity: 1;
  }
  
  .group:hover .card-modern {
    transform: none;
  }
  
  * {
    transition-duration: 0.01ms !important;
  }
}

/* Amélioration du contraste en mode sombre */
.dark .card-modern {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

/* Effet de glow subtil pour certaines cartes importantes */
.group:hover .card-modern[class*="from-blue-"] {
  box-shadow: 
    0 20px 25px -5px rgba(59, 130, 246, 0.1),
    0 10px 10px -5px rgba(59, 130, 246, 0.05);
}

.group:hover .card-modern[class*="from-red-"] {
  box-shadow: 
    0 20px 25px -5px rgba(239, 68, 68, 0.1),
    0 10px 10px -5px rgba(239, 68, 68, 0.05);
}

.group:hover .card-modern[class*="from-emerald-"] {
  box-shadow: 
    0 20px 25px -5px rgba(16, 185, 129, 0.1),
    0 10px 10px -5px rgba(16, 185, 129, 0.05);
}
</style>