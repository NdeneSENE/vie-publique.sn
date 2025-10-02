<template>
  <div class="py-6 sm:py-8">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8" itemscope itemtype="https://schema.org/CollectionPage">
      <!-- Breadcrumb moderne -->
      <nav class="mb-8">
        <NuxtLink
          to="/assemblee-nationale"
          class="group inline-flex items-center rounded-full bg-white px-4 py-2 text-sm font-medium text-gray-700 shadow-sm ring-1 ring-gray-200 transition-all duration-200 hover:bg-gray-50 hover:shadow-md hover:ring-gray-300 dark:bg-gray-800 dark:text-gray-300 dark:ring-gray-700 dark:hover:bg-gray-700 dark:hover:ring-gray-600"
        >
          <UIcon
            name="i-heroicons-arrow-left"
            class="mr-2 h-4 w-4 transition-transform duration-200 group-hover:-translate-x-0.5"
          />
          15e législature
        </NuxtLink>
      </nav>

      <!-- Header de section moderne -->
      <div class="text-center mb-8">
        <h1
          class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white"
          itemprop="headline"
        >
          Votes parlementaires
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4 max-w-3xl mx-auto">
          Décryptage et contextualisation des votes de la 15e législature pour une meilleure compréhension de l'activité parlementaire
        </p>
      </div>

      <!-- Content section -->
      <div class="votes-container">
        <!-- Loading state moderne -->
        <div v-if="loading && !votes.length" class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div
            v-for="n in 6"
            :key="n"
            class="vote-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 100}ms` }"
          >
            <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <div class="p-6">
                <!-- Header skeleton -->
                <div class="mb-4 flex items-center justify-between">
                  <div class="h-6 w-20 rounded-full bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-6 w-16 rounded-full bg-gray-200 dark:bg-gray-600"></div>
                </div>

                <!-- Content skeleton -->
                <div class="space-y-3">
                  <div class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-4 w-24 rounded bg-gray-200 dark:bg-gray-600"></div>
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
            @click="fetchAssemblyVotes()"
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
          >
            Réessayer
          </button>
        </div>

        <!-- Empty state -->
        <div
          v-else-if="!votes.length"
          class="text-center py-12"
        >
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-hand-raised" class="h-8 w-8 text-gray-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Aucun vote disponible
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Les votes parlementaires seront affichés ici
          </p>
        </div>

        <!-- Grid des votes -->
        <div v-else class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <article
            v-for="(vote, index) in votes"
            :key="vote.id"
            class="vote-card group relative"
            :style="{ animationDelay: `${index * 100}ms` }"
          >
            <NuxtLink
              :to="`/assemblee-nationale/votes/${vote.id}`"
              class="block h-full"
            >
              <div class="vote-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                <div class="p-6">
                  <!-- Header avec type et statut -->
                  <div class="mb-4 flex items-start justify-between">
                    <!-- Type de vote -->
                    <div class="flex items-center gap-2">
                      <div class="flex h-8 w-8 items-center justify-center rounded-lg bg-blue-100 dark:bg-blue-900/30">
                        <UIcon
                          name="i-heroicons-hand-raised"
                          class="h-4 w-4 text-blue-600 dark:text-blue-400"
                        />
                      </div>
                      <span class="text-xs font-medium text-gray-600 dark:text-gray-400">
                        {{ $getAssemblyVoteLabel(vote.type) }}
                      </span>
                    </div>

                    <!-- Statut du vote -->
                    <span
                      class="inline-flex items-center rounded-full px-2.5 py-1 text-xs font-medium transition-transform duration-200 group-hover:scale-105"
                      :class="vote.status === 'adopted'
                        ? 'bg-emerald-100 text-emerald-800 dark:bg-emerald-900/30 dark:text-emerald-400'
                        : 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-400'"
                    >
                      {{ vote.status === 'adopted' ? 'Adopté' : 'Rejeté' }}
                    </span>
                  </div>

                  <!-- Titre du vote -->
                  <h3 class="mb-4 text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400 line-clamp-3">
                    {{ vote.name }}
                  </h3>

                  <!-- Footer avec date et flèche -->
                  <div class="flex items-center justify-between">
                    <div class="flex items-center text-sm text-gray-500 dark:text-gray-400">
                      <UIcon name="i-heroicons-calendar-days" class="mr-1.5 h-4 w-4" />
                      <time>{{ $dateformat(vote.date) }}</time>
                    </div>

                    <!-- Flèche de navigation -->
                    <div class="flex h-7 w-7 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:scale-110 group-hover:bg-blue-50 dark:bg-gray-700 dark:group-hover:bg-blue-900/30">
                      <UIcon
                        name="i-heroicons-arrow-up-right"
                        class="h-3.5 w-3.5 text-gray-400 transition-colors duration-300 group-hover:text-blue-600 dark:group-hover:text-blue-400"
                      />
                    </div>
                  </div>
                </div>

                <!-- Effet de border animé -->
                <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20"></div>

                <!-- Indicateur de statut colorisé -->
                <div
                  class="absolute left-0 top-0 h-1 w-full transition-all duration-300 group-hover:h-2"
                  :class="vote.status === 'adopted'
                    ? 'bg-emerald-500'
                    : 'bg-red-500'"
                ></div>
              </div>
            </NuxtLink>
          </article>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Votes parlementaires de l'Assemblée nationale du Sénégal | 15e législature";
const description = "Suivez et analysez tous les votes parlementaires de la 15e législature. Décryptage et contextualisation pour une meilleure compréhension de l'activité législative.";
const url = `${siteUrl}/assemblee-nationale/votes`;
const image = `${siteUrl}/images/votes-assemblee-senegal.webp`;

const votesCollectionSchema = {
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
    name: "Assemblée nationale du Sénégal",
    description: "Parlement de la République du Sénégal",
    url: `${siteUrl}/assemblee-nationale`,
  },
  mainEntity: {
    "@type": "ItemList",
    name: "Votes parlementaires",
    description: "Collection des votes de l'Assemblée nationale du Sénégal",
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
      name: "Assemblée nationale",
      item: `${siteUrl}/assemblee-nationale`,
    },
    {
      "@type": "ListItem",
      position: 3,
      name: "Votes",
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
    "votes parlementaires Sénégal",
    "Assemblée nationale votes",
    "législation Sénégal",
    "15e législature votes",
    "activité parlementaire Sénégal",
    "démocratie Sénégal",
    "transparence parlementaire",
  ].join(", "),
});

useHead({
  htmlAttrs: { lang: "fr-SN" },
  link: [{ rel: "canonical", href: url }],
  meta: [
    { name: "theme-color", content: themeColor },
    { name: "author", content: "Assemblée nationale du Sénégal" },
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
      children: JSON.stringify(votesCollectionSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

const { fetchAssemblyVotes, votes, loading, error } = useAssemblyVotes();

// Chargement des données au montage
onMounted(() => {
  fetchAssemblyVotes();
});
</script>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes voteCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.vote-card {
  animation: voteCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.vote-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .vote-card-inner {
  transform: translateY(-4px);
}

/* Skeleton loading animation */
.vote-skeleton {
  animation: voteCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Line clamp pour les titres longs */
.line-clamp-3 {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  line-height: 1.5;
  max-height: 4.5em;
}

/* États focus pour l'accessibilité */
.vote-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .vote-card-inner {
    border-radius: 1rem;
  }

  .vote-card-inner .p-6 {
    padding: 1.25rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .vote-card {
    animation: none;
    opacity: 1;
  }

  .group:hover .vote-card-inner {
    transform: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.vote-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .vote-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .vote-card {
    break-inside: avoid;
  }

  .vote-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .vote-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

/* Glow effect pour les badges de statut */
.group:hover [class*="bg-emerald-"] {
  box-shadow: 0 0 20px rgba(34, 197, 94, 0.3);
}

.group:hover [class*="bg-red-"] {
  box-shadow: 0 0 20px rgba(239, 68, 68, 0.3);
}

/* Animation pour l'indicateur de statut */
.vote-card:hover [class*="bg-emerald-500"],
.vote-card:hover [class*="bg-red-500"] {
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
}
</style>
