<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Documents budgétaires du Sénégal | Budget de l'État";
const description = "Consultez tous les documents budgétaires du Sénégal : lois de finances, budgets ministériels, rapports d'exécution budgétaire et analyses financières.";
const url = `${siteUrl}/documents/budget`;
const image = `${siteUrl}/images/documents-budget-senegal.webp`;

const budgetSchema = {
  "@context": "https://schema.org",
  "@type": "CollectionPage",
  name: title,
  description: description,
  url: url,
  image: image,
  isPartOf: {
    "@type": "WebSite",
    name: siteName,
    url: siteUrl,
  },
  about: {
    "@type": "GovernmentOrganization",
    name: "République du Sénégal",
    description: "Documents budgétaires officiels",
    url: siteUrl,
  },
  mainEntity: {
    "@type": "ItemList",
    name: "Documents budgétaires",
    description: "Collection des documents budgétaires officiels du Sénégal",
  },
};

const breadcrumbSchema = {
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  itemListElement: [
    {
      "@type": "ListItem",
      position: 1,
      name: "Accueil",
      item: siteUrl,
    },
    {
      "@type": "ListItem",
      position: 2,
      name: "Documents",
      item: `${siteUrl}/documents`,
    },
    {
      "@type": "ListItem",
      position: 3,
      name: "Budget",
      item: url,
    },
  ],
};

useSeoMeta({
  title,
  ogTitle: title,
  description,
  ogDescription: description,
  ogImage: image,
  ogUrl: url,
  twitterCard: "summary_large_image",
  twitterTitle: title,
  twitterDescription: description,
  twitterImage: image,
  keywords: [
    ...keywords,
    "documents budgétaires Sénégal",
    "budget État Sénégal",
    "loi de finances Sénégal",
    "finances publiques Sénégal",
    "budget ministériel",
    "exécution budgétaire",
    "transparence budgétaire",
  ].join(", "),
});

useHead({
  htmlAttrs: { lang: "fr-SN" },
  link: [{ rel: "canonical", href: url }],
  meta: [
    { name: "theme-color", content: themeColor },
    { name: "author", content: "République du Sénégal" },
    { property: "og:type", content: "website" },
    { property: "og:site_name", content: siteName },
    { name: "robots", content: "index, follow" },
    { name: "geo.region", content: "SN" },
    { name: "geo.placename", content: "Dakar" },
    { name: "geo.position", content: "14.7645042;-17.3660286" },
    { name: "ICBM", content: "14.7645042, -17.3660286" },
  ],
  script: [
    {
      type: "application/ld+json",
      children: JSON.stringify(budgetSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

const { documents, loading, error } = useDocuments({ type: "budget" });
const searchQuery = ref("");
const itemsPerPage = ref(6);
const currentPage = ref(1);

const filteredDocuments = computed(() => {
  if (!documents.value) return [];
  const searchLower = searchQuery.value.toLowerCase();
  return documents.value.filter(
    (doc) =>
      doc.title?.toLowerCase().includes(searchLower) ||
      (doc as any).description?.toLowerCase().includes(searchLower),
  );
});

const paginatedDocuments = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return filteredDocuments.value.slice(start, end);
});

// Fonction pour gérer le changement de page
const handlePageChange = (page: number) => {
  currentPage.value = page;
  // Faire défiler vers le haut de la liste
  window.scrollTo({ top: 0, behavior: "smooth" });
};
</script>

<template>
  <div class="py-6 sm:py-8">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8" itemscope itemtype="https://schema.org/CollectionPage">
      <!-- Breadcrumb moderne -->
      <nav class="mb-8">
        <NuxtLink
          to="/documents"
          class="group inline-flex items-center rounded-full bg-white px-4 py-2 text-sm font-medium text-gray-700 shadow-sm ring-1 ring-gray-200 transition-all duration-200 hover:bg-gray-50 hover:shadow-md hover:ring-gray-300 dark:bg-gray-800 dark:text-gray-300 dark:ring-gray-700 dark:hover:bg-gray-700 dark:hover:ring-gray-600"
        >
          <UIcon
            name="i-heroicons-arrow-left"
            class="mr-2 h-4 w-4 transition-transform duration-200 group-hover:-translate-x-0.5"
          />
          Documents
        </NuxtLink>
      </nav>

      <!-- Header de section moderne -->
      <div class="text-center mb-12">
        <div class="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-full bg-emerald-100 dark:bg-emerald-900/30">
          <UIcon name="i-heroicons-banknotes" class="h-8 w-8 text-emerald-600 dark:text-emerald-400" />
        </div>
        <h1
          class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white"
          itemprop="headline"
        >
          Documents budgétaires
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4 max-w-3xl mx-auto">
          Transparence budgétaire et financière. Consultez les lois de finances, budgets ministériels et rapports d'exécution budgétaire
        </p>
      </div>

      <!-- Section de recherche moderne -->
      <div class="mb-12">
        <div class="mx-auto max-w-2xl">
          <div class="relative">
            <UInput
              v-model="searchQuery"
              size="lg"
              placeholder="Rechercher un document budgétaire..."
              icon="i-heroicons-magnifying-glass"
              class="w-full shadow-sm"
              :ui="{
                icon: { leading: { pointer: '' } },
                base: 'relative block w-full disabled:cursor-not-allowed disabled:opacity-75 focus:outline-none border-0',
                rounded: 'rounded-xl',
                placeholder: 'placeholder-gray-400 dark:placeholder-gray-500',
                size: { lg: 'text-lg' },
                gap: { lg: 'gap-x-3' },
                padding: { lg: 'px-4 py-4' },
              }"
            />
          </div>

          <div class="mt-4 text-center">
            <p class="text-sm text-gray-600 dark:text-gray-400">
              <span class="font-medium">{{ filteredDocuments.length }}</span> documents budgétaires disponibles
            </p>
          </div>
        </div>
      </div>

      <!-- Loading state moderne -->
      <div v-if="loading" class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        <div
          v-for="n in 6"
          :key="n"
          class="budget-skeleton animate-pulse"
          :style="{ animationDelay: `${n * 100}ms` }"
        >
          <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="h-48 bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
              <div class="skeleton-shimmer h-full w-full"></div>
            </div>
            <div class="p-6">
              <div class="mb-4 flex items-center gap-3">
                <div class="h-6 w-20 rounded-full bg-gray-200 dark:bg-gray-600"></div>
              </div>
              <div class="space-y-3">
                <div class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                <div class="mt-4 flex items-center justify-between">
                  <div class="h-4 w-20 rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-7 w-7 rounded-full bg-gray-200 dark:bg-gray-600"></div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Error state moderne -->
      <div v-else-if="error" class="text-center py-12">
        <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-red-100 dark:bg-red-900/30">
          <UIcon name="i-heroicons-exclamation-triangle" class="h-8 w-8 text-red-600 dark:text-red-400" />
        </div>
        <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
          Erreur de chargement
        </h3>
        <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
          {{ error }}
        </p>
        <button
          @click="$router.go(0)"
          class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
        >
          Réessayer
        </button>
      </div>

      <!-- Empty state -->
      <div
        v-else-if="!paginatedDocuments.length"
        class="text-center py-12"
      >
        <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
          <UIcon name="i-heroicons-banknotes" class="h-8 w-8 text-gray-400" />
        </div>
        <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
          Aucun document budgétaire
        </h3>
        <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
          Les documents budgétaires seront affichés ici
        </p>
      </div>

      <!-- Grid des documents -->
      <div v-else class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        <article
          v-for="(doc, index) in paginatedDocuments"
          :key="doc.id"
          class="budget-document group relative"
          :style="{ animationDelay: `${index * 100}ms` }"
        >
          <NuxtLink
            :to="`/documents/${doc.id}/${doc.slug}`"
            class="block h-full"
          >
            <div class="budget-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
              <!-- Image header -->
              <div class="relative h-48 overflow-hidden bg-gradient-to-br from-emerald-50 to-emerald-100 dark:from-emerald-900/20 dark:to-emerald-800/20">
                <div class="absolute inset-0 bg-gradient-to-br from-emerald-500/10 to-emerald-600/10"></div>

                <!-- Image par défaut ou image de couverture -->
                <template v-if="doc.cover_image">
                  <img
                    :src="$directusImageUrl(doc.cover_image, '400')"
                    :alt="`Aperçu ${doc.title}`"
                    class="h-full w-full object-cover transition-transform duration-500 group-hover:scale-105"
                    loading="lazy"
                    @error="$event.target.style.display = 'none'"
                  />
                </template>

                <!-- Design par défaut avec patterns -->
                <div class="flex h-full items-center justify-center relative">
                  <!-- Pattern de fond -->
                  <div class="absolute inset-0 opacity-10">
                    <div class="grid grid-cols-8 gap-2 h-full p-4">
                      <div v-for="i in 32" :key="i" class="bg-emerald-500 rounded-sm" :style="{ height: Math.random() * 100 + '%' }"></div>
                    </div>
                  </div>

                  <!-- Icône principale -->
                  <div class="relative z-10 flex flex-col items-center">
                    <div class="bg-emerald-100 dark:bg-emerald-900/50 rounded-full p-4 mb-2">
                      <UIcon name="i-heroicons-banknotes" class="h-8 w-8 text-emerald-600 dark:text-emerald-400" />
                    </div>
                    <span class="text-xs font-medium text-emerald-700 dark:text-emerald-300">Document budgétaire</span>
                  </div>
                </div>

                <!-- Overlay gradient -->
                <div class="absolute inset-0 bg-gradient-to-t from-black/20 via-transparent to-transparent"></div>

                <!-- Badge type -->
                <div class="absolute top-4 left-4">
                  <span class="inline-flex items-center rounded-full bg-emerald-100 px-3 py-1 text-xs font-medium text-emerald-800 dark:bg-emerald-900/30 dark:text-emerald-400">
                    Budget
                  </span>
                </div>
              </div>

              <div class="p-6">
                <!-- Titre du document -->
                <h3 class="mb-3 text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-emerald-600 dark:text-white dark:group-hover:text-emerald-400 line-clamp-2">
                  {{ doc.title }}
                </h3>

                <!-- Footer avec date et flèche -->
                <div class="flex items-center justify-between">
                  <div class="flex items-center text-sm text-gray-500 dark:text-gray-400">
                    <UIcon name="i-heroicons-calendar-days" class="mr-1.5 h-4 w-4" />
                    <time>{{ $dateMonthYearformat(doc.publish_date) }}</time>
                  </div>

                  <!-- Flèche de navigation -->
                  <div class="flex h-7 w-7 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:scale-110 group-hover:bg-emerald-50 dark:bg-gray-700 dark:group-hover:bg-emerald-900/30">
                    <UIcon
                      name="i-heroicons-arrow-up-right"
                      class="h-3.5 w-3.5 text-gray-400 transition-colors duration-300 group-hover:text-emerald-600 dark:group-hover:text-emerald-400"
                    />
                  </div>
                </div>
              </div>

              <!-- Effet de border animé -->
              <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-emerald-500/20 dark:ring-white/10 dark:group-hover:ring-emerald-400/20"></div>
            </div>
          </NuxtLink>
        </article>
      </div>

      <!-- Pagination moderne -->
      <div
        v-if="filteredDocuments.length > itemsPerPage"
        class="mt-12 flex flex-col items-center gap-6 sm:flex-row sm:justify-between"
      >
        <div class="flex items-center gap-3">
          <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Afficher</span>
          <USelect
            v-model="itemsPerPage"
            :options="[6, 12, 24]"
            size="sm"
            class="w-20"
          />
          <span class="text-sm font-medium text-gray-700 dark:text-gray-300">par page</span>
        </div>

        <UPagination
          v-model="currentPage"
          :total="filteredDocuments.length"
          :per-page="itemsPerPage"
          :active-button="{ color: 'emerald' }"
          :ui="{
            wrapper: 'flex items-center gap-1',
            base: 'min-w-9 min-h-9 flex items-center justify-center rounded-lg focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none disabled:cursor-not-allowed transition-colors',
            active: 'bg-emerald-600 text-white shadow-sm',
            inactive: 'bg-white text-gray-700 hover:bg-gray-50 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700 border border-gray-300 dark:border-gray-600',
          }"
          @change="handlePageChange"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Animation d'apparition des documents */
@keyframes budgetDocumentFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.budget-document {
  animation: budgetDocumentFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.budget-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .budget-card-inner {
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
.budget-skeleton {
  animation: budgetDocumentFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Line clamp pour les titres longs */
.line-clamp-2 {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  line-height: 1.5;
  max-height: 3em;
}

/* États focus pour l'accessibilité */
.budget-document a:focus-visible {
  outline: 2px solid #10b981;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .budget-card-inner {
    border-radius: 1rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .budget-document {
    animation: none;
    opacity: 1;
  }

  .group:hover .budget-card-inner {
    transform: none;
  }

  .skeleton-shimmer {
    animation: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.budget-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .budget-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .budget-document {
    break-inside: avoid;
  }

  .budget-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .budget-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}
</style>
