<template>
  <div class="py-8 sm:py-12">
    <!-- Header de section moderne -->
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <div class="text-center">
        <h2 class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white">
          Documents Officiels
        </h2>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4">
          Accédez aux documents officiels du Sénégal : lois, décrets, rapports et codes généraux
        </p>
      </div>

      <!-- Grille des cartes avec animation séquentielle -->
      <div class="mt-8 sm:mt-12">
        <div class="grid grid-cols-1 gap-4 sm:grid-cols-2 sm:gap-6 lg:grid-cols-3 xl:gap-8">
          <NuxtLink
            v-for="(document, index) in documentCategories"
            :key="document.title"
            :to="document.to"
            class="group relative block"
            :style="{ animationDelay: `${index * 100}ms` }"
          >
            <!-- Carte principale -->
            <div
              class="card-modern relative overflow-hidden rounded-2xl border border-gray-200/60 bg-gradient-to-br transition-all duration-300 ease-out dark:border-gray-700/60"
              :class="[
                getCardConfig(document.title).gradient,
                getCardConfig(document.title).hoverGradient
              ]"
            >
              <!-- Effet de brillance au hover -->
              <div class="absolute inset-0 bg-gradient-to-r from-transparent via-white/10 to-transparent opacity-0 transition-opacity duration-500 group-hover:opacity-100 dark:via-white/5" />
              
              <!-- Badge "Nouveau" si applicable -->
              <div 
                v-if="document.isNew"
                class="absolute right-3 top-3 z-10"
              >
                <span class="inline-flex items-center rounded-full bg-blue-600 px-2 py-1 text-xs font-medium text-white shadow-sm dark:bg-blue-500">
                  Nouveau
                </span>
              </div>

              <!-- Badge personnalisé -->
              <div 
                v-if="document.badge"
                class="absolute right-3 top-3 z-10"
              >
                <span class="inline-flex items-center rounded-full bg-red-600 px-2 py-1 text-xs font-medium text-white shadow-sm dark:bg-red-500">
                  {{ document.badge }}
                </span>
              </div>

              <!-- Contenu de la carte -->
              <div class="relative p-6">
                <!-- Container de l'icône avec effet de background -->
                <div class="flex items-start space-x-4">
                  <div 
                    class="flex h-12 w-12 flex-shrink-0 items-center justify-center rounded-xl bg-gradient-to-br transition-all duration-300 group-hover:scale-110 group-hover:rotate-3"
                    :class="getCardConfig(document.title).bgColor"
                  >
                    <UIcon
                      :name="document.icon"
                      class="h-6 w-6 transition-all duration-300"
                      :class="getCardConfig(document.title).iconColor"
                    />
                  </div>

                  <!-- Texte -->
                  <div class="min-w-0 flex-1">
                    <h3 class="text-lg font-semibold text-gray-900 transition-colors duration-200 group-hover:text-gray-700 dark:text-white dark:group-hover:text-gray-200">
                      {{ document.title }}
                    </h3>
                    <p 
                      v-if="document.description"
                      class="mt-2 text-sm text-gray-600 transition-colors duration-200 group-hover:text-gray-500 dark:text-gray-400 dark:group-hover:text-gray-300"
                    >
                      {{ document.description }}
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

      <!-- Section des derniers documents -->
      <div class="mt-16">
        <HomeLatestDocuments />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import HomeLatestDocuments from "~/components/HomeLatestDocuments.vue";

useHead({
  title: "Documents officiels du Sénégal",
  meta: [
    {
      name: "description",
      content:
        "Accédez aux documents officiels du Sénégal: Journal officiel, rapports d'audit, codes généraux et plus encore.",
    },
  ],
});

const documentCategories = [
  {
    title: "Journal Officiel",
    description: "Textes, Lois, Décrets, Arrêtés",
    icon: "i-heroicons-newspaper",
    to: "/documents/journal-officiel",
    color: "blue",
  },
  {
    title: "Rapports Publics",
    description: "Cours des Comptes OFNAC etc",
    icon: "i-heroicons-document-chart-bar",
    to: "/documents/rapports-audit",
    color: "green",
  },
  {
    title: "Documents Budgétaires",
    description: "Lois de finances et annexes",
    icon: "i-heroicons-banknotes",
    to: "/documents/budget",
    color: "amber",
  },
  {
    title: "Documents Stratégie",
    description: "Les stratégies du Sénégal",
    icon: "i-heroicons-presentation-chart-line",
    to: "/documents/strategies",
    color: "red",
  },
  {
    title: "Codes Généraux",
    description: "Constitution, famille, presse...",
    icon: "i-heroicons-scale",
    to: "/documents/codes",
    color: "indigo",
  },
  {
    title: "Tous les documents",
    description: "Liste complète des documents officiels",
    icon: "i-heroicons-document-text",
    to: "/documents/public",
    color: "purple",
  },
];

// Configuration des thèmes par carte avec gradients modernes
const cardConfigs = {
  "Journal Officiel": {
    gradient: "from-blue-50 to-indigo-50 dark:from-blue-950/30 dark:to-indigo-950/30",
    hoverGradient: "group-hover:from-blue-100 group-hover:to-indigo-100 dark:group-hover:from-blue-900/40 dark:group-hover:to-indigo-900/40",
    iconColor: "text-blue-600 dark:text-blue-400",
    bgColor: "bg-blue-500/10 dark:bg-blue-400/10"
  },
  "Rapports Publics": {
    gradient: "from-emerald-50 to-teal-50 dark:from-emerald-950/30 dark:to-teal-950/30",
    hoverGradient: "group-hover:from-emerald-100 group-hover:to-teal-100 dark:group-hover:from-emerald-900/40 dark:group-hover:to-teal-900/40",
    iconColor: "text-emerald-600 dark:text-emerald-400",
    bgColor: "bg-emerald-500/10 dark:bg-emerald-400/10"
  },
  "Documents Budgétaires": {
    gradient: "from-amber-50 to-orange-50 dark:from-amber-950/30 dark:to-orange-950/30",
    hoverGradient: "group-hover:from-amber-100 group-hover:to-orange-100 dark:group-hover:from-amber-900/40 dark:group-hover:to-orange-900/40",
    iconColor: "text-amber-600 dark:text-amber-400",
    bgColor: "bg-amber-500/10 dark:bg-amber-400/10"
  },
  "Documents Stratégie": {
    gradient: "from-red-50 to-rose-50 dark:from-red-950/30 dark:to-rose-950/30",
    hoverGradient: "group-hover:from-red-100 group-hover:to-rose-100 dark:group-hover:from-red-900/40 dark:group-hover:to-rose-900/40",
    iconColor: "text-red-600 dark:text-red-400",
    bgColor: "bg-red-500/10 dark:bg-red-400/10"
  },
  "Codes Généraux": {
    gradient: "from-indigo-50 to-purple-50 dark:from-indigo-950/30 dark:to-purple-950/30",
    hoverGradient: "group-hover:from-indigo-100 group-hover:to-purple-100 dark:group-hover:from-indigo-900/40 dark:group-hover:to-purple-900/40",
    iconColor: "text-indigo-600 dark:text-indigo-400",
    bgColor: "bg-indigo-500/10 dark:bg-indigo-400/10"
  },
  "Tous les documents": {
    gradient: "from-purple-50 to-violet-50 dark:from-purple-950/30 dark:to-violet-950/30",
    hoverGradient: "group-hover:from-purple-100 group-hover:to-violet-100 dark:group-hover:from-purple-900/40 dark:group-hover:to-violet-900/40",
    iconColor: "text-purple-600 dark:text-purple-400",
    bgColor: "bg-purple-500/10 dark:bg-purple-400/10"
  },
};

// Configuration par défaut pour les cartes non configurées
const defaultConfig = {
  gradient: "from-gray-50 to-slate-50 dark:from-gray-950/30 dark:to-slate-950/30",
  hoverGradient: "group-hover:from-gray-100 group-hover:to-slate-100 dark:group-hover:from-gray-900/40 dark:group-hover:to-slate-900/40",
  iconColor: "text-gray-600 dark:text-gray-400",
  bgColor: "bg-gray-500/10 dark:bg-gray-400/10"
};

// Fonction pour obtenir la configuration d'une carte
const getCardConfig = (title: string) => {
  return cardConfigs[title] || defaultConfig;
};
</script>

<style scoped>
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

.group:hover .card-modern[class*="from-amber-"] {
  box-shadow: 
    0 20px 25px -5px rgba(245, 158, 11, 0.1),
    0 10px 10px -5px rgba(245, 158, 11, 0.05);
}

.group:hover .card-modern[class*="from-indigo-"] {
  box-shadow: 
    0 20px 25px -5px rgba(99, 102, 241, 0.1),
    0 10px 10px -5px rgba(99, 102, 241, 0.05);
}

.group:hover .card-modern[class*="from-purple-"] {
  box-shadow: 
    0 20px 25px -5px rgba(147, 51, 234, 0.1),
    0 10px 10px -5px rgba(147, 51, 234, 0.05);
}
</style>