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
          Groupes parlementaires
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4 max-w-3xl mx-auto">
          Organisation politique de l'Assemblée nationale. Chaque groupe rassemble des députés selon leur affinité politique (minimum 16 membres)
        </p>
      </div>

      <!-- Content section -->
      <div class="groups-container">
        <!-- Loading state moderne -->
        <div v-if="loading && !groups.length" class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div
            v-for="n in 6"
            :key="n"
            class="group-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 100}ms` }"
          >
            <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <div class="p-6">
                <!-- Header skeleton -->
                <div class="mb-4 flex items-center gap-3">
                  <div class="h-12 w-12 rounded-xl bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                    <div class="skeleton-shimmer h-full w-full rounded-xl"></div>
                  </div>
                  <div class="flex-1 space-y-2">
                    <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                    <div class="h-3 w-1/2 rounded bg-gray-200 dark:bg-gray-600"></div>
                  </div>
                </div>

                <!-- Content skeleton -->
                <div class="space-y-3">
                  <div class="h-4 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-4 w-2/3 rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="mt-4 flex items-center justify-between">
                    <div class="h-4 w-20 rounded bg-gray-200 dark:bg-gray-600"></div>
                    <div class="h-5 w-5 rounded bg-gray-200 dark:bg-gray-600"></div>
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
            @click="fetchAssemblyGroups()"
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
          >
            Réessayer
          </button>
        </div>

        <!-- Empty state -->
        <div
          v-else-if="!groups.length"
          class="text-center py-12"
        >
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-user-group" class="h-8 w-8 text-gray-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Aucun groupe disponible
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Les groupes parlementaires seront affichés ici
          </p>
        </div>

        <!-- Utilisation du composant existant avec wrapper moderne -->
        <div v-else class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div
            v-for="(group, index) in groups"
            :key="group.id"
            class="group-wrapper"
            :style="{ animationDelay: `${index * 100}ms` }"
          >
            <AssemblyGroupCard :group="group" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Groupes parlementaires de l'Assemblée nationale du Sénégal | 15e législature";
const description = "Découvrez l'organisation politique de l'Assemblée nationale. Composition, dirigeants et membres des groupes parlementaires de la 15e législature.";
const url = `${siteUrl}/assemblee-nationale/groupes`;
const image = `${siteUrl}/images/groupes-parlementaires-senegal.webp`;

const groupsCollectionSchema = {
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
    name: "Groupes parlementaires",
    description: "Organisation politique des députés par affinité politique",
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
      name: "Groupes parlementaires",
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
    "groupes parlementaires Sénégal",
    "Assemblée nationale groupes",
    "partis politiques Sénégal",
    "15e législature groupes",
    "organisation politique parlement",
    "majorité opposition Sénégal",
    "députés groupes politiques",
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
      children: JSON.stringify(groupsCollectionSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

const { groups, loading, error, fetchAssemblyGroups } = useAssemblyGroups();

// Chargement des données au montage
onMounted(() => {
  fetchAssemblyGroups();
});
</script>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes groupCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.group-wrapper {
  animation: groupCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
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
.group-skeleton {
  animation: groupCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .groups-container {
    padding: 0 0.5rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .group-wrapper {
    animation: none;
    opacity: 1;
  }

  .skeleton-shimmer {
    animation: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.groups-container {
  contain: layout style paint;
}

/* Print styles */
@media print {
  .group-wrapper {
    break-inside: avoid;
  }
}

/* Dark mode enhancements */
.dark .groups-container {
  color-scheme: dark;
}
</style>
