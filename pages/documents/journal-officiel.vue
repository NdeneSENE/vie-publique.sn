<!-- index.vue -->
<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Journal Officiel du Sénégal | Textes officiels et réglementaires";
const description = "Accédez à tous les numéros du Journal Officiel du Sénégal. Textes législatifs, réglementaires, décrets, arrêtés et actes officiels.";
const url = `${siteUrl}/documents/journal-officiel`;
const image = `${siteUrl}/images/journal-officiel-senegal.webp`;

const journalSchema = {
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
    description: "Journal Officiel - Publication des textes législatifs et réglementaires",
    url: siteUrl,
  },
  mainEntity: {
    "@type": "ItemList",
    name: "Journal Officiel",
    description: "Archives numériques du Journal Officiel du Sénégal",
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
      name: "Journal Officiel",
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
    "Journal Officiel Sénégal",
    "JO Sénégal",
    "textes officiels Sénégal",
    "décrets Sénégal",
    "arrêtés Sénégal",
    "législation Sénégal",
    "réglementation Sénégal",
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
      children: JSON.stringify(journalSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

import { useRouter } from "vue-router";
import { useJournalOfficielStore } from "~/stores/journalOfficiel";
import { useDebounceFn } from "@vueuse/core";
import { useJournalOfficiel } from "~/composables/useJournalOfficiel";

interface Document {
  id: string;
  title?: string;
  publish_date: string;
  slug?: string;
  jo_number?: string;
  description?: string;
}

const store = useJournalOfficielStore();
const _router = useRouter();
const config = useRuntimeConfig();

// Utiliser le composable
const { documents, loading, error, updateSearch, updateYear, updatePage } =
  useJournalOfficiel();

// Utiliser les valeurs du store
const searchQuery = computed({
  get: () => store.searchQuery,
  set: (value) => {
    store.setSearchQuery(value);
    store.setCurrentPage(1);
    debouncedSearch(value);
  },
});

const selectedYear = computed({
  get: () => store.selectedYear,
  set: (value) => {
    store.setSelectedYear(value);
    store.setCurrentPage(1);
    updateYear(value);
  },
});

const currentPage = computed({
  get: () => store.currentPage,
  set: (value) => {
    store.setCurrentPage(value);
    updatePage(value);
  },
});

// Options pour le sélecteur d'années
const yearOptions = [
  { label: "Toutes les années", value: "all" },
  { label: "2025", value: "2025" },
  { label: "2024", value: "2024" },
  { label: "2023", value: "2023" },
  { label: "2022", value: "2022" },
  { label: "2021", value: "2021" },
  { label: "2020", value: "2020" },
  { label: "2019", value: "2019" },
  { label: "2018", value: "2018" },
  { label: "2017", value: "2017" },
  { label: "2016", value: "2016" },
  // { label: "2015", value: "2015" },
  // { label: "2014", value: "2014" },
  // { label: "2013", value: "2013" },
  // { label: "2012", value: "2012" },
  // { label: "2011", value: "2011" },
  // { label: "2010", value: "2010" },
];

// Fonction pour charger les documents
const fetchDocuments = async () => {
  try {
    loading.value = true;
    store.setLoading(true);
    error.value = null;

    // Construire les paramètres de requête
    const params = new URLSearchParams({
      filter: JSON.stringify({
        status: "published",
        type: "official_journal",
      }),
      page: store.currentPage.toString(),
      limit: store.itemsPerPage.toString(),
    });

    // Ajouter la recherche si présente
    if (searchQuery.value) {
      params.append("search", searchQuery.value);
    }

    // Ajouter le filtre par année si sélectionnée
    if (selectedYear.value !== "all") {
      const year = parseInt(selectedYear.value);
      params.append(
        "filter",
        JSON.stringify({
          status: "published",
          type: "official_journal",
          publish_date: {
            _gte: `${year}-01-01`,
            _lte: `${year}-12-31`,
          },
        }),
      );
    }

    // Récupérer le nombre total de documents
    const totalResponse = await fetch(
      `${config.public.cmsApiUrl}/items/documents?aggregate[countDistinct]=id&filter[status]=published&filter[type]=official_journal`,
      {
        headers: {
          Authorization: `Bearer ${config.public.cmsApiKey}`,
        },
      },
    );

    if (!totalResponse.ok) {
      throw new Error(
        "Erreur lors de la récupération du nombre total de documents",
      );
    }

    const totalData = await totalResponse.json();
    store.setTotalItems(totalData.data[0].count);

    // Récupérer les documents paginés
    const response = await fetch(
      `${config.public.cmsApiUrl}/items/documents?${params.toString()}`,
      {
        headers: {
          Authorization: `Bearer ${config.public.cmsApiKey}`,
        },
      },
    );

    if (!response.ok) {
      throw new Error("Erreur lors de la récupération des documents");
    }

    const data = await response.json();
    documents.value = data.data;
  } catch (e) {
    error.value = e instanceof Error ? e.message : "Une erreur est survenue";
    documents.value = [];
  } finally {
    loading.value = false;
    store.setLoading(false);
  }
};

// Debounce pour la recherche
const debouncedSearch = useDebounceFn((query: string) => {
  updateSearch(query);
}, 500);

// Format de la date
const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString("fr-FR", {
    day: "numeric",
    month: "long",
    year: "numeric",
  });
};

// Texte pour l'affichage du nombre de résultats
const resultsText = computed(() => {
  const totalCount = store.totalItems;
  const currentPageStart = (store.currentPage - 1) * store.itemsPerPage + 1;
  const currentPageEnd = Math.min(
    currentPageStart + store.itemsPerPage - 1,
    totalCount,
  );

  // Construction des suffixes conditionnels
  const yearText =
    selectedYear.value !== "all" ? ` en ${selectedYear.value}` : "";
  const searchText = store.searchQuery ? ` pour "${store.searchQuery}"` : "";

  if (totalCount === 0) {
    return store.searchQuery
      ? `Aucun résultat trouvé pour "${store.searchQuery}"`
      : "Aucun résultat";
  }

  if (totalCount === 1) {
    return `1 Journal trouvé${searchText}${yearText}`;
  }

  if (totalCount <= store.itemsPerPage) {
    return `${totalCount} Journaux trouvés${searchText}${yearText}`;
  }

  return `${currentPageStart}-${currentPageEnd} sur ${totalCount} journaux${searchText}${yearText}`;
});
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
        <div class="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-full bg-amber-100 dark:bg-amber-900/30">
          <UIcon name="i-heroicons-newspaper" class="h-8 w-8 text-amber-600 dark:text-amber-400" />
        </div>
        <h1
          class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white"
          itemprop="headline"
        >
          Journal Officiel du Sénégal
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4 max-w-3xl mx-auto">
          Archives officielles et publication des textes législatifs. Consultez tous les numéros du Journal Officiel depuis 2016
        </p>
      </div>

    <!-- Section de recherche et filtres modernes -->
    <div class="mb-12">
      <div class="mx-auto max-w-4xl">
        <div class="flex flex-col gap-4 sm:flex-row">
          <div class="flex-1">
            <UInput
              v-model="searchQuery"
              size="lg"
              placeholder="Rechercher par numéro, date ou contenu..."
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

          <div class="w-full sm:w-48">
            <USelect
              v-model="selectedYear"
              :options="yearOptions"
              placeholder="Année"
              size="lg"
              class="shadow-sm"
              :ui="{
                base: 'relative block w-full disabled:cursor-not-allowed disabled:opacity-75 focus:outline-none border-0',
                rounded: 'rounded-xl',
                size: { lg: 'text-lg' },
                padding: { lg: 'px-4 py-4' },
              }"
            />
          </div>
        </div>

        <div class="mt-6 text-center">
          <p class="text-sm text-gray-600 dark:text-gray-400">
            {{ resultsText }}
          </p>
        </div>
      </div>
    </div>

    <!-- Loading state moderne -->
    <div v-if="loading" class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
      <div
        v-for="n in 6"
        :key="n"
        class="journal-skeleton animate-pulse"
        :style="{ animationDelay: `${n * 100}ms` }"
      >
        <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
          <div class="flex gap-4 p-6">
            <div class="flex-shrink-0">
              <div class="h-24 w-16 rounded-lg bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                <div class="skeleton-shimmer h-full w-full rounded-lg"></div>
              </div>
            </div>
            <div class="flex-1 space-y-3">
              <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
              <div class="h-4 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
              <div class="h-4 w-1/2 rounded bg-gray-200 dark:bg-gray-600"></div>
              <div class="flex items-center gap-2 mt-3">
                <div class="h-4 w-4 rounded bg-gray-200 dark:bg-gray-600"></div>
                <div class="h-4 w-20 rounded bg-gray-200 dark:bg-gray-600"></div>
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
        Une erreur s'est produite lors du chargement des journaux
      </p>
      <button
        @click="fetchDocuments"
        class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
      >
        Réessayer
      </button>
    </div>

    <!-- Empty state -->
    <div
      v-else-if="documents.length === 0"
      class="text-center py-12"
    >
      <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
        <UIcon name="i-heroicons-inbox" class="h-8 w-8 text-gray-400" />
      </div>
      <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
        Aucun résultat
      </h3>
      <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
        Aucun journal officiel ne correspond à votre recherche
      </p>
    </div>

    <!-- Grid des journaux -->
    <div v-else class="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
      <article
        v-for="(journal, index) in documents"
        :key="journal.id"
        class="journal-card group relative"
        :style="{ animationDelay: `${index * 100}ms` }"
      >
        <NuxtLink
          :to="`/documents/${journal.id}/${journal.slug || 'journal-officiel'}`"
          class="block h-full"
        >
          <div class="journal-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
            <div class="flex gap-4 p-6">
              <!-- Image du journal -->
              <div class="flex-shrink-0">
                <div class="relative h-24 w-16 overflow-hidden rounded-lg bg-gradient-to-br from-amber-50 to-amber-100 dark:from-amber-900/20 dark:to-amber-800/20">
                  <img
                    src="/images/default-journal-officiel.webp"
                    :alt="`Aperçu JO ${journal.jo_number || ''}`"
                    class="h-full w-full object-cover transition-transform duration-500 group-hover:scale-105"
                    loading="lazy"
                  />

                  <!-- Badge numéro -->
                  <div v-if="journal.jo_number" class="absolute top-1 left-1">
                    <span class="inline-flex items-center rounded bg-amber-100 px-1.5 py-0.5 text-xs font-medium text-amber-800 dark:bg-amber-900/30 dark:text-amber-400">
                      N°{{ journal.jo_number }}
                    </span>
                  </div>
                </div>
              </div>

              <!-- Contenu -->
              <div class="flex-1 min-w-0">
                <!-- Titre -->
                <h3 class="mb-2 text-sm font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-amber-600 dark:text-white dark:group-hover:text-amber-400 line-clamp-2">
                  {{ journal.title }}
                </h3>

                <!-- Description -->
                <p class="mb-3 text-xs text-gray-600 dark:text-gray-400 line-clamp-2">
                  {{ journal.description }}
                </p>

                <!-- Footer -->
                <div class="flex items-center justify-between">
                  <div class="flex items-center text-xs text-gray-500 dark:text-gray-400">
                    <UIcon name="i-heroicons-calendar-days" class="mr-1 h-3 w-3" />
                    <time>{{ formatDate(journal.publish_date) }}</time>
                  </div>

                  <!-- Flèche de navigation -->
                  <div class="flex h-6 w-6 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:scale-110 group-hover:bg-amber-50 dark:bg-gray-700 dark:group-hover:bg-amber-900/30">
                    <UIcon
                      name="i-heroicons-arrow-up-right"
                      class="h-3 w-3 text-gray-400 transition-colors duration-300 group-hover:text-amber-600 dark:group-hover:text-amber-400"
                    />
                  </div>
                </div>
              </div>
            </div>

            <!-- Effet de border animé -->
            <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-amber-500/20 dark:ring-white/10 dark:group-hover:ring-amber-400/20"></div>
          </div>
        </NuxtLink>
      </article>
    </div>

    <!-- Pagination moderne -->
    <div v-if="store.totalPages > 1" class="mt-12 flex justify-center">
      <UPagination
        v-model="currentPage"
        :total="store.totalItems"
        :default-page="1"
        :show-edges="true"
        :sibling-count="2"
        :active-button="{ color: 'amber' }"
        :ui="{
          wrapper: 'flex items-center gap-1',
          base: 'min-w-9 min-h-9 flex items-center justify-center rounded-lg focus:outline-none focus:ring-2 focus:ring-amber-500 focus:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none disabled:cursor-not-allowed transition-colors',
          active: 'bg-amber-600 text-white shadow-sm',
          inactive: 'bg-white text-gray-700 hover:bg-gray-50 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700 border border-gray-300 dark:border-gray-600',
        }"
      />
    </div>
    </div>
  </div>
</template>

<style scoped>
/* Animation d'apparition des journaux */
@keyframes journalCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.journal-card {
  animation: journalCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.journal-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .journal-card-inner {
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
.journal-skeleton {
  animation: journalCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Line clamp pour les titres longs */
.line-clamp-2 {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  line-height: 1.4;
  max-height: 2.8em;
}

/* États focus pour l'accessibilité */
.journal-card a:focus-visible {
  outline: 2px solid #f59e0b;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .journal-card-inner {
    border-radius: 1rem;
  }

  .journal-card-inner .p-6 {
    padding: 1.25rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .journal-card {
    animation: none;
    opacity: 1;
  }

  .group:hover .journal-card-inner {
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
.journal-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .journal-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .journal-card {
    break-inside: avoid;
  }

  .journal-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .journal-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}
</style>
