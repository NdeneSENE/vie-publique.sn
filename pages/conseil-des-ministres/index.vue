<script setup lang="ts">
import { useConseilMinistres } from "~/composables/useConseilMinistres";
import { useConseilMinistresStore } from "~/stores/conseilMinistres";
import { useDebounceFn } from "@vueuse/core";

const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();

const title = "Conseil des ministres du Sénégal | Communiqués officiels";
const description =
  "Suivez les communiqués du Conseil des ministres du gouvernement sénégalais. Décisions, nominations et orientations du gouvernement du Sénégal.";
const url = `${siteUrl}/conseil-des-ministres`;
const image = `${siteUrl}/images/share-conseil-des-ministres-nomination-full.jfif`;

// Schemas SEO (gardés identiques)
const conseilMinistresSchema = {
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
    name: "Conseil des ministres du Sénégal",
    description:
      "Organe exécutif du gouvernement sénégalais présidé par le Président de la République",
    parentOrganization: {
      "@type": "GovernmentOrganization",
      name: "République du Sénégal",
    },
  },
  mainEntity: {
    "@type": "ItemList",
    name: "Communiqués du Conseil des ministres",
    description:
      "Collection des communiqués officiels du Conseil des ministres du Sénégal",
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
      name: "Conseil des ministres",
      item: url,
    },
  ],
};

// Configuration SEO complète
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
    "Conseil des ministres Sénégal",
    "communiqué conseil des ministres",
    "gouvernement Sénégal",
    "décisions gouvernementales",
    "nominations gouvernement Sénégal",
    "Bassirou Diomaye Faye",
    "Ousmane Sonko",
    "politique sénégalaise",
  ].join(", "),
});

useHead({
  htmlAttrs: { lang: "fr-SN" },
  link: [{ rel: "canonical", href: url }],
  meta: [
    { name: "theme-color", content: themeColor },
    { name: "author", content: "Conseil des ministres du Sénégal" },
    { property: "og:type", content: "website" },
    { property: "og:site_name", content: siteName },
    { name: "robots", content: "index, follow" },
    { name: "geo.region", content: "SN" },
    { name: "geo.placename", content: "Dakar" },
    { name: "geo.position", content: "14.7645042;-17.3660286" },
    { name: "ICBM", content: "14.7645042, -17.3660286" },
    {
      name: "news_keywords",
      content: "Conseil des ministres, Sénégal, gouvernement, communiqué",
    },
  ],
  script: [
    {
      type: "application/ld+json",
      children: JSON.stringify(conseilMinistresSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

const store = useConseilMinistresStore();
const { news, loading, error, updateSearch, updatePage } =
  useConseilMinistres();

// Store computed values
const searchQuery = computed({
  get: () => store.searchQuery,
  set: (value) => {
    store.setSearchQuery(value);
    store.setCurrentPage(1);
    debouncedSearch(value);
  },
});

const currentPage = computed({
  get: () => store.currentPage,
  set: (value) => {
    store.setCurrentPage(value);
    updatePage(value);
  },
});

// Debounce pour la recherche
const debouncedSearch = useDebounceFn((query: string) => {
  updateSearch(query);
}, 500);

// Texte pour l'affichage du nombre de résultats
const resultsText = computed(() => {
  const totalCount = store.totalItems;
  const currentPageStart = (store.currentPage - 1) * store.itemsPerPage + 1;
  const currentPageEnd = Math.min(
    currentPageStart + store.itemsPerPage - 1,
    totalCount,
  );

  const searchText = store.searchQuery ? ` pour "${store.searchQuery}"` : "";

  if (totalCount === 0) {
    return store.searchQuery
      ? `Aucun résultat trouvé${searchText}`
      : "Aucun communiqué disponible";
  }

  if (totalCount === 1) {
    return `1 communiqué trouvé${searchText}`;
  }

  if (totalCount <= store.itemsPerPage) {
    return `${totalCount} communiqués trouvés${searchText}`;
  }

  return `${currentPageStart}-${currentPageEnd} sur ${totalCount} communiqués${searchText}`;
});

const formatDateISO = (date: string) => {
  return new Date(date).toISOString();
};
</script>

<template>
  <div class="py-6 sm:py-8">
    <div
      class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8"
      itemscope
      itemtype="https://schema.org/CollectionPage"
    >
      <!-- Header épuré -->
      <div class="mb-8 text-center">
        <h1
          class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white"
          itemprop="headline"
        >
          Conseil des ministres
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300">
          Communiqués officiels du gouvernement sénégalais
        </p>
      </div>

      <!-- Schema.org metadata (gardées cachées) -->
      <div
        itemprop="about"
        itemscope
        itemtype="https://schema.org/GovernmentOrganization"
        class="hidden"
      >
        <meta itemprop="name" content="Conseil des ministres du Sénégal" />
        <meta itemprop="url" :content="url" />

        <div
          itemprop="parentOrganization"
          itemscope
          itemtype="https://schema.org/GovernmentOrganization"
        >
          <meta itemprop="name" content="République du Sénégal" />
        </div>

        <div itemprop="leader" itemscope itemtype="https://schema.org/Person">
          <meta itemprop="name" content="Bassirou Diomaye Faye" />
          <meta
            itemprop="jobTitle"
            content="Président de la République du Sénégal"
          />
        </div>
      </div>

      <!-- Barre de recherche moderne -->
      <div class="mb-8">
        <div class="mx-auto max-w-xl">
          <div class="relative">
            <div
              class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-4"
            >
              <UIcon
                name="i-heroicons-magnifying-glass"
                class="h-5 w-5 text-gray-400"
              />
            </div>
            <input
              v-model="searchQuery"
              type="search"
              placeholder="Rechercher un communiqué..."
              class="w-full rounded-xl border-0 bg-white py-3 pl-12 pr-4 text-gray-900 shadow-sm ring-1 ring-gray-200 placeholder:text-gray-400 focus:ring-2 focus:ring-amber-500 focus:ring-offset-2 sm:text-sm dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:placeholder:text-gray-500 dark:focus:ring-amber-400"
              :disabled="loading"
            />
          </div>
        </div>

        <!-- Compteur de résultats -->
        <div class="mt-3 text-center">
          <span class="text-sm text-gray-500 dark:text-gray-400">
            {{ resultsText }}
          </span>
        </div>
      </div>

      <!-- Contenu principal -->
      <div>
        <!-- Loading state -->
        <div
          v-if="loading"
          class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3"
        >
          <div
            v-for="n in 9"
            :key="n"
            class="conseil-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 100}ms` }"
          >
            <div
              class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700"
            >
              <!-- Image skeleton -->
              <div
                class="aspect-[16/9] bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700"
              >
                <div class="skeleton-shimmer h-full w-full"></div>
              </div>

              <!-- Content skeleton -->
              <div class="p-4">
                <div class="space-y-2">
                  <div
                    class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"
                  ></div>
                  <div
                    class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"
                  ></div>
                </div>
                <div
                  class="mt-3 h-4 w-32 rounded bg-gray-200 dark:bg-gray-600"
                ></div>
              </div>
            </div>
          </div>
        </div>

        <!-- Error state -->
        <div v-else-if="error" class="py-12 text-center">
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
            {{ error }}
          </p>
          <button
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
            @click="updateSearch(searchQuery)"
          >
            Réessayer
          </button>
        </div>

        <!-- Empty state -->
        <div v-else-if="news.length === 0" class="py-12 text-center">
          <div
            class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-amber-100 dark:bg-amber-900/30"
          >
            <UIcon
              name="i-heroicons-document-magnifying-glass"
              class="h-8 w-8 text-amber-600 dark:text-amber-400"
            />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Aucun communiqué trouvé
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Essayez de modifier vos critères de recherche
          </p>
        </div>

        <!-- Grille des communiqués -->
        <div v-else>
          <div
            class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3"
            itemscope
            itemtype="https://schema.org/ItemList"
            itemprop="mainEntity"
          >
            <meta itemprop="numberOfItems" :content="news.length" />

            <article
              v-for="(item, index) in news"
              :key="item.id"
              itemscope
              itemtype="https://schema.org/GovernmentAnnouncement"
              itemprop="itemListElement"
              class="conseil-card group relative"
              :style="{ animationDelay: `${index * 100}ms` }"
            >
              <meta itemprop="position" :content="index + 1" />
              <meta
                itemprop="url"
                :content="`${siteUrl}/conseil-des-ministres/${item.id}/${item.slug}`"
              />
              <meta
                itemprop="datePublished"
                :content="formatDateISO(item.date_published)"
              />

              <div
                itemprop="publisher"
                itemscope
                itemtype="https://schema.org/GovernmentOrganization"
              >
                <meta
                  itemprop="name"
                  content="Conseil des ministres du Sénégal"
                />
              </div>

              <NuxtLink
                :to="`/conseil-des-ministres/${item.id}/${item.slug}`"
                class="block h-full"
                itemprop="url"
              >
                <div
                  class="conseil-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600"
                >
                  <!-- Image avec overlay -->
                  <div class="relative aspect-[16/9] overflow-hidden">
                    <div
                      itemprop="image"
                      itemscope
                      itemtype="https://schema.org/ImageObject"
                    >
                      <NuxtImg
                        :src="
                          item.cover_image
                            ? $directusImageUrl(item.cover_image, '50')
                            : '/images/communique-conseil-des-ministres.jpeg'
                        "
                        :alt="
                          item.title || 'Communiqué du conseil des ministres'
                        "
                        class="h-full w-full object-cover transition-transform duration-300 group-hover:scale-105"
                        loading="lazy"
                        fetchpriority="high"
                        sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
                        :placeholder="[400, 225]"
                        itemprop="contentUrl"
                      />
                      <meta
                        itemprop="url"
                        :content="
                          item.cover_image
                            ? $directusImageUrl(item.cover_image, '50')
                            : '/images/communique-conseil-des-ministres.jpeg'
                        "
                      />
                      <meta itemprop="width" content="400" />
                      <meta itemprop="height" content="225" />
                    </div>

                    <!-- Overlay gradient -->
                    <div
                      class="absolute inset-0 bg-gradient-to-t from-black/20 via-transparent to-transparent opacity-0 transition-opacity duration-300 group-hover:opacity-100"
                    ></div>

                    <!-- Badge gouvernemental -->
                    <div class="absolute left-4 top-4">
                      <span
                        class="inline-flex items-center rounded-full bg-black/60 px-3 py-1 text-xs font-medium text-white shadow-lg backdrop-blur-sm"
                      >
                        <div class="mr-2 h-2 w-2 rounded-full bg-amber-500" />
                        Gouvernement
                      </span>
                    </div>
                  </div>

                  <!-- Contenu -->
                  <div class="p-4">
                    <h2
                      class="line-clamp-2 text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-amber-600 dark:text-white dark:group-hover:text-amber-400"
                      itemprop="headline"
                    >
                      {{ item.title || "Communiqué du conseil des ministres" }}
                    </h2>

                    <div
                      class="mt-3 flex items-center text-sm text-gray-500 dark:text-gray-400"
                    >
                      <UIcon
                        name="i-heroicons-calendar-days"
                        class="mr-1.5 h-4 w-4"
                      />
                      <time
                        v-if="item.date_published"
                        :datetime="formatDateISO(item.date_published)"
                        itemprop="datePublished"
                      >
                        {{ $dateformatWithDayName(item.date_published) }}
                      </time>
                    </div>

                    <!-- Metadata cachées -->
                    <meta
                      itemprop="name"
                      :content="
                        item.title || 'Communiqué du conseil des ministres'
                      "
                    />
                    <meta
                      itemprop="category"
                      content="Communiqué gouvernemental"
                    />
                    <meta
                      itemprop="about"
                      content="Conseil des ministres du Sénégal"
                    />
                  </div>

                  <!-- Effet de border animé -->
                  <div
                    class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-amber-500/20 dark:ring-white/10 dark:group-hover:ring-amber-400/20"
                  ></div>
                </div>
              </NuxtLink>
            </article>
          </div>

          <!-- Pagination moderne -->
          <div v-if="store.totalPages > 1" class="mt-12 flex justify-center">
            <nav class="flex items-center space-x-2">
              <button
                :disabled="currentPage <= 1"
                class="inline-flex h-10 w-10 items-center justify-center rounded-lg bg-white text-gray-500 shadow-sm ring-1 ring-gray-200 hover:bg-gray-50 disabled:cursor-not-allowed disabled:opacity-50 dark:bg-gray-800 dark:text-gray-400 dark:ring-gray-700 dark:hover:bg-gray-700"
                @click="currentPage = Math.max(1, currentPage - 1)"
              >
                <UIcon name="i-heroicons-chevron-left" class="h-4 w-4" />
              </button>

              <div class="flex items-center space-x-1">
                <button
                  v-for="page in Math.min(5, store.totalPages)"
                  :key="page"
                  :class="
                    page === currentPage
                      ? 'bg-amber-600 text-white shadow-sm'
                      : 'bg-white text-gray-700 hover:bg-gray-50 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700'
                  "
                  class="inline-flex h-10 w-10 items-center justify-center rounded-lg text-sm font-medium ring-1 ring-gray-200 dark:ring-gray-700"
                  @click="currentPage = page"
                >
                  {{ page }}
                </button>
              </div>

              <button
                :disabled="currentPage >= store.totalPages"
                class="inline-flex h-10 w-10 items-center justify-center rounded-lg bg-white text-gray-500 shadow-sm ring-1 ring-gray-200 hover:bg-gray-50 disabled:cursor-not-allowed disabled:opacity-50 dark:bg-gray-800 dark:text-gray-400 dark:ring-gray-700 dark:hover:bg-gray-700"
                @click="
                  currentPage = Math.min(store.totalPages, currentPage + 1)
                "
              >
                <UIcon name="i-heroicons-chevron-right" class="h-4 w-4" />
              </button>
            </nav>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes conseilCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.conseil-card {
  animation: conseilCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.conseil-card-inner {
  transition:
    transform 0.3s ease-out,
    box-shadow 0.3s ease-out;
}

.group:hover .conseil-card-inner {
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
.conseil-skeleton {
  animation: conseilCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* États focus pour l'accessibilité */
.conseil-card a:focus-visible {
  outline: 2px solid #d97706;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .conseil-card-inner {
    border-radius: 1rem;
  }

  .conseil-card-inner .p-4 {
    padding: 1rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .conseil-card {
    animation: none;
    opacity: 1;
  }

  .skeleton-shimmer {
    animation: none;
  }

  .group:hover .conseil-card-inner {
    transform: none;
  }

  .group:hover img {
    transform: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.conseil-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .conseil-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .conseil-card {
    break-inside: avoid;
  }

  .conseil-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .conseil-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

/* Line clamp fallback */
@supports not (-webkit-line-clamp: 2) {
  .line-clamp-2 {
    overflow: hidden;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    line-height: 1.5;
    max-height: 3em;
  }
}

/* Glow effect pour les cartes gouvernementales */
.group:hover .conseil-card-inner {
  box-shadow:
    0 20px 25px -5px rgba(217, 119, 6, 0.1),
    0 10px 10px -5px rgba(217, 119, 6, 0.05);
}

.dark .group:hover .conseil-card-inner {
  box-shadow:
    0 20px 25px -5px rgba(217, 119, 6, 0.2),
    0 10px 10px -5px rgba(217, 119, 6, 0.1);
}
</style>
