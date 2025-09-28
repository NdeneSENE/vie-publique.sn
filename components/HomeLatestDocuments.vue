<script setup lang="ts">
import { useLatestUpdatesStore } from "~/stores/latestUpdates";

const store = useLatestUpdatesStore();

// Fonction pour formater la date
const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString("fr-FR", {
    day: "numeric",
    month: "long",
    year: "numeric",
  });
};

// Fonction pour formater la date relative
const formatRelativeDate = (date: string) => {
  const now = new Date();
  const docDate = new Date(date);
  const diffTime = Math.abs(now.getTime() - docDate.getTime());
  const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));

  if (diffDays === 1) return "Aujourd'hui";
  if (diffDays === 2) return "Hier";
  if (diffDays <= 7) return `Il y a ${diffDays - 1} jours`;
  if (diffDays <= 30)
    return `Il y a ${Math.floor((diffDays - 1) / 7)} semaines`;
  return formatDate(date);
};

// Fonction pour obtenir l'icône selon le type de document
const getDocumentIcon = (title: string) => {
  const titleLower = title.toLowerCase();

  if (titleLower.includes("budget") || titleLower.includes("finance")) {
    return {
      icon: "i-heroicons-banknotes",
      color: "text-emerald-600",
      bg: "bg-emerald-50 dark:bg-emerald-900/30",
    };
  }
  if (titleLower.includes("loi") || titleLower.includes("décret")) {
    return {
      icon: "i-heroicons-scale",
      color: "text-blue-600",
      bg: "bg-blue-50 dark:bg-blue-900/30",
    };
  }
  if (titleLower.includes("rapport") || titleLower.includes("étude")) {
    return {
      icon: "i-heroicons-chart-bar",
      color: "text-purple-600",
      bg: "bg-purple-50 dark:bg-purple-900/30",
    };
  }
  if (titleLower.includes("procès") || titleLower.includes("séance")) {
    return {
      icon: "i-heroicons-document-text",
      color: "text-amber-600",
      bg: "bg-amber-50 dark:bg-amber-900/30",
    };
  }

  return {
    icon: "i-heroicons-document",
    color: "text-gray-600 dark:text-gray-400",
    bg: "bg-gray-50 dark:bg-gray-900/30",
  };
};

// Fonction pour obtenir le badge de catégorie
const getDocumentCategory = (title: string) => {
  const titleLower = title.toLowerCase();

  if (titleLower.includes("budget"))
    return {
      text: "Budget",
      color:
        "bg-emerald-100 text-emerald-800 dark:bg-emerald-900/30 dark:text-emerald-400",
    };
  if (titleLower.includes("loi"))
    return {
      text: "Législation",
      color: "bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-400",
    };
  if (titleLower.includes("rapport"))
    return {
      text: "Rapport",
      color:
        "bg-purple-100 text-purple-800 dark:bg-purple-900/30 dark:text-purple-400",
    };
  if (titleLower.includes("conseil"))
    return {
      text: "Conseil",
      color:
        "bg-amber-100 text-amber-800 dark:bg-amber-900/30 dark:text-amber-400",
    };
  if (titleLower.includes("assemblée"))
    return {
      text: "Assemblée",
      color:
        "bg-indigo-100 text-indigo-800 dark:bg-indigo-900/30 dark:text-indigo-400",
    };

  return {
    text: "Document",
    color: "bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-400",
  };
};

onMounted(() => {
  store.fetchUpdates();
});
</script>

<template>
  <div class="py-8 sm:py-12">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <!-- Header de section moderne -->
      <div class="text-center">
        <h2
          class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white"
        >
          Derniers documents ajoutés
        </h2>
        <p class="mt-3 text-lg text-gray-600 sm:mt-4 dark:text-gray-300">
          Consultez les dernières publications officielles
        </p>
      </div>

      <!-- Content section -->
      <div class="mt-8 sm:mt-12">
        <!-- Loading state -->
        <div
          v-if="store.isLoading"
          class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3"
        >
          <div
            v-for="n in 6"
            :key="n"
            class="document-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 100}ms` }"
          >
            <div
              class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700"
            >
              <div class="p-6">
                <!-- Header skeleton -->
                <div class="mb-4 flex items-center justify-between">
                  <div class="flex items-center gap-3">
                    <div
                      class="h-10 w-10 rounded-xl bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700"
                    >
                      <div
                        class="skeleton-shimmer h-full w-full rounded-xl"
                      ></div>
                    </div>
                    <div
                      class="h-4 w-16 rounded-full bg-gray-200 dark:bg-gray-600"
                    ></div>
                  </div>
                  <div
                    class="h-5 w-20 rounded-full bg-gray-200 dark:bg-gray-600"
                  ></div>
                </div>

                <!-- Content skeleton -->
                <div class="space-y-2">
                  <div
                    class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"
                  ></div>
                  <div
                    class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"
                  ></div>
                </div>

                <!-- Footer skeleton -->
                <div class="mt-4 flex items-center justify-between">
                  <div
                    class="h-4 w-24 rounded bg-gray-200 dark:bg-gray-600"
                  ></div>
                  <div
                    class="h-6 w-6 rounded-full bg-gray-200 dark:bg-gray-600"
                  ></div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Error state -->
        <div v-else-if="store.hasError" class="py-12 text-center">
          <div
            class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-red-100 dark:bg-red-900/30"
          >
            <UIcon
              name="i-heroicons-exclamation-triangle"
              class="h-8 w-8 text-red-600 dark:text-red-400"
            />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Erreur de chargement
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Une erreur est survenue lors du chargement des documents.
          </p>
          <button
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
            @click="store.fetchUpdates()"
          >
            Réessayer
          </button>
        </div>

        <!-- Documents grid -->
        <div v-else>
          <div class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
            <article
              v-for="(document, index) in store.getLatestDocuments"
              :key="document.id"
              class="document-card group relative"
              :style="{ animationDelay: `${index * 100}ms` }"
            >
              <NuxtLink :to="document.url" class="block h-full">
                <div
                  class="document-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600"
                >
                  <div class="p-6">
                    <!-- Header avec icône et badge -->
                    <div class="mb-4 flex items-start justify-between">
                      <div class="flex items-center gap-3">
                        <!-- Icône dynamique -->
                        <div
                          class="flex h-10 w-10 flex-shrink-0 items-center justify-center rounded-xl transition-all duration-300 group-hover:scale-110"
                          :class="getDocumentIcon(document.title).bg"
                        >
                          <UIcon
                            :name="getDocumentIcon(document.title).icon"
                            class="h-5 w-5 transition-colors duration-300"
                            :class="getDocumentIcon(document.title).color"
                          />
                        </div>

                        <!-- Date relative -->
                        <div class="text-xs text-gray-500 dark:text-gray-400">
                          {{ formatRelativeDate(document.date_created) }}
                        </div>
                      </div>

                      <!-- Badge de catégorie -->
                      <span
                        class="inline-flex items-center rounded-full px-2.5 py-1 text-xs font-medium transition-transform duration-200 group-hover:scale-105"
                        :class="getDocumentCategory(document.title).color"
                      >
                        {{ getDocumentCategory(document.title).text }}
                      </span>
                    </div>

                    <!-- Titre du document -->
                    <h3
                      class="line-clamp-3 text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400"
                    >
                      {{ document.title }}
                    </h3>

                    <!-- Footer avec métadonnées -->
                    <div class="mt-4 flex items-center justify-between">
                      <div
                        class="flex items-center text-xs text-gray-500 dark:text-gray-400"
                      >
                        <UIcon
                          name="i-heroicons-calendar-days"
                          class="mr-1.5 h-3 w-3"
                        />
                        {{ formatDate(document.date_created) }}
                      </div>

                      <!-- Flèche de navigation -->
                      <div
                        class="flex h-7 w-7 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:scale-110 group-hover:bg-blue-50 dark:bg-gray-700 dark:group-hover:bg-blue-900/30"
                      >
                        <UIcon
                          name="i-heroicons-arrow-up-right"
                          class="h-3.5 w-3.5 text-gray-400 transition-colors duration-300 group-hover:text-blue-600 dark:group-hover:text-blue-400"
                        />
                      </div>
                    </div>
                  </div>

                  <!-- Effet de border animé -->
                  <div
                    class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20"
                  ></div>

                  <!-- Effet de brillance -->
                  <div
                    class="absolute inset-0 bg-gradient-to-r from-transparent via-white/5 to-transparent opacity-0 transition-opacity duration-500 group-hover:opacity-100"
                  ></div>
                </div>
              </NuxtLink>
            </article>
          </div>

          <!-- Call to action -->
          <div class="mt-12 text-center">
            <NuxtLink
              to="/documents/public"
              class="group inline-flex items-center rounded-full bg-white px-6 py-3 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 transition-all duration-200 hover:bg-gray-50 hover:shadow-md hover:ring-gray-400 dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:hover:bg-gray-700 dark:hover:ring-gray-600"
            >
              Voir tous les documents
              <UIcon
                name="i-heroicons-arrow-right"
                class="ml-2 h-4 w-4 transition-transform duration-200 group-hover:translate-x-1"
              />
            </NuxtLink>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes documentCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.document-card {
  animation: documentCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.document-card-inner {
  transition:
    transform 0.3s ease-out,
    box-shadow 0.3s ease-out;
}

.group:hover .document-card-inner {
  transform: translateY(-4px);
}

/* Animation de shimmer pour le skeleton */
@keyframes shimmer {
  0% {
    background-position: -200px 0;
  }
  100% {
    background-position: calc(200px + 100%) 0;
  }
}

.skeleton-shimmer {
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.4),
    transparent
  );
  background-size: 200px 100%;
  animation: shimmer 1.5s infinite;
}

.dark .skeleton-shimmer {
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.1),
    transparent
  );
}

/* Skeleton loading animation */
.document-skeleton {
  animation: documentCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Micro-animations pour les badges */
.group:hover [class*="bg-emerald-"],
.group:hover [class*="bg-blue-"],
.group:hover [class*="bg-purple-"],
.group:hover [class*="bg-amber-"] {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

/* États focus pour l'accessibilité */
.document-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .document-card-inner {
    border-radius: 1rem;
  }

  .document-card-inner .p-6 {
    padding: 1.25rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .document-card {
    animation: none;
    opacity: 1;
  }

  .skeleton-shimmer {
    animation: none;
  }

  .group:hover .document-card-inner {
    transform: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Amélioration du contraste */
@media (prefers-contrast: high) {
  .document-card-inner {
    border: 2px solid;
  }

  .text-gray-500 {
    color: rgb(75, 85, 99);
  }

  .dark .text-gray-400 {
    color: rgb(156, 163, 175);
  }
}

/* Performance optimizations */
.document-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .document-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .document-card {
    break-inside: avoid;
  }

  .document-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .document-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

/* Line clamp fallback pour les anciens navigateurs */
@supports not (-webkit-line-clamp: 3) {
  .line-clamp-3 {
    overflow: hidden;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    line-height: 1.5;
    max-height: 4.5em;
  }
}

/* Glow effects pour différents types de documents */
.group:hover [class*="bg-emerald-50"] {
  box-shadow: 0 0 20px rgba(16, 185, 129, 0.2);
}

.group:hover [class*="bg-blue-50"] {
  box-shadow: 0 0 20px rgba(59, 130, 246, 0.2);
}

.group:hover [class*="bg-purple-50"] {
  box-shadow: 0 0 20px rgba(147, 51, 234, 0.2);
}

.group:hover [class*="bg-amber-50"] {
  box-shadow: 0 0 20px rgba(245, 158, 11, 0.2);
}

/* Hover effect pour les titres */
.group:hover h3 {
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.dark .group:hover h3 {
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}
</style>
