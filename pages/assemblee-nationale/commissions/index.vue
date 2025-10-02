<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();

const title =
  "Commissions de l'Assemblée nationale du Sénégal | 15e législature";
const description =
  "Découvrez les commissions parlementaires de l'Assemblée nationale du Sénégal. Organisation, présidents et membres des commissions de la 15e législature.";
const url = `${siteUrl}/assemblee-nationale/commissions`;
const image = `${siteUrl}/images/commissions-assemblee-senegal.webp`;

const commissionsCollectionSchema = {
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
    name: "Commissions parlementaires",
    description: "Liste des commissions de l'Assemblée nationale du Sénégal",
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
      name: "Commissions",
      item: url,
    },
  ],
};

const organizationSchema = {
  "@context": "https://schema.org",
  "@type": "LegislativeBuilding",
  name: "Assemblée nationale du Sénégal",
  url: `${siteUrl}/assemblee-nationale`,
  description:
    "Institution législative avec ses commissions parlementaires spécialisées",
  address: {
    "@type": "PostalAddress",
    streetAddress: "Avenue Léopold Sédar Senghor",
    addressLocality: "Dakar",
    addressCountry: "SN",
  },
  governmentType: "Legislature",
  numberOfMembers: 165,
  legislativeTerm: "15e législature",
  subOrganization: {
    "@type": "GovernmentOrganization",
    name: "Commissions parlementaires",
    description: "Organes spécialisés de l'Assemblée nationale",
  },
};

const governmentServiceSchema = {
  "@context": "https://schema.org",
  "@type": "GovernmentService",
  name: "Commissions parlementaires du Sénégal",
  description:
    "Services des commissions spécialisées de l'Assemblée nationale pour l'examen des projets de loi",
  provider: {
    "@type": "GovernmentOrganization",
    name: "Assemblée nationale du Sénégal",
  },
  areaServed: {
    "@type": "Country",
    name: "Sénégal",
  },
  serviceType: "Travail législatif",
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
    "commissions parlementaires Sénégal",
    "Assemblée nationale commissions",
    "commissions législatives Sénégal",
    "15e législature commissions",
    "travail parlementaire Sénégal",
    "présidents commissions Assemblée",
    "membres commissions députés",
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
      children: JSON.stringify(commissionsCollectionSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(organizationSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(governmentServiceSchema),
    },
  ],
});

const { commissions, loading, error } = useAssemblyCommissions();

const searchQuery = ref("");

const filteredCommissions = computed(() => {
  if (!commissions.value) return [];

  if (!searchQuery.value.trim()) {
    return commissions.value;
  }

  const query = searchQuery.value.toLowerCase();
  return commissions.value.filter((commission) => {
    const nameMatch = commission.name.toLowerCase().includes(query);
    const presidentMatch = commission.president
      ? `${commission.president.first_name} ${commission.president.last_name}`.toLowerCase().includes(query)
      : false;

    return nameMatch || presidentMatch;
  });
});
</script>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes commissionCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.commission-card {
  animation: commissionCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.commission-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .commission-card-inner {
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
.commission-skeleton {
  animation: commissionCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* États focus pour l'accessibilité */
.commission-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .commission-card-inner {
    border-radius: 1rem;
  }

  .commission-card-inner .p-6 {
    padding: 1.25rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .commission-card {
    animation: none;
    opacity: 1;
  }

  .skeleton-shimmer {
    animation: none;
  }

  .group:hover .commission-card-inner {
    transform: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.commission-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .commission-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .commission-card {
    break-inside: avoid;
  }

  .commission-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .commission-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

/* Glow effect pour les icônes */
.group:hover [class*="bg-blue-100"] {
  box-shadow: 0 0 20px rgba(59, 130, 246, 0.2);
}

.group:hover [class*="bg-green-100"] {
  box-shadow: 0 0 20px rgba(34, 197, 94, 0.2);
}
</style>

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
          Commissions de l'Assemblée
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4">
          Organisation et travaux des commissions parlementaires spécialisées
        </p>
      </div>

      <!-- Barre de recherche moderne -->
      <div class="mb-8">
        <div class="mx-auto max-w-xl">
          <div class="relative">
            <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-4">
              <UIcon name="i-heroicons-magnifying-glass" class="h-5 w-5 text-gray-400" />
            </div>
            <input
              v-model="searchQuery"
              type="search"
              placeholder="Rechercher une commission..."
              class="w-full rounded-xl border-0 bg-white py-3 pl-12 pr-4 text-gray-900 shadow-sm ring-1 ring-gray-200 placeholder:text-gray-400 focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 sm:text-sm dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:placeholder:text-gray-500 dark:focus:ring-blue-400"
              :disabled="loading"
            />
          </div>
        </div>
      </div>

      <!-- Content section -->
      <div class="commissions-container">
        <!-- Loading state moderne -->
        <div v-if="loading" class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div
            v-for="n in 6"
            :key="n"
            class="commission-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 100}ms` }"
          >
            <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <div class="p-6">
                <!-- Header skeleton -->
                <div class="mb-4 flex items-center gap-3">
                  <div class="h-10 w-10 rounded-xl bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                    <div class="skeleton-shimmer h-full w-full rounded-xl"></div>
                  </div>
                  <div class="flex-1 space-y-2">
                    <div class="h-4 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                    <div class="h-3 w-2/3 rounded bg-gray-200 dark:bg-gray-600"></div>
                  </div>
                </div>

                <!-- Content skeleton -->
                <div class="space-y-3">
                  <div class="h-3 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-3 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="mt-4 flex items-center justify-between">
                    <div class="h-3 w-20 rounded bg-gray-200 dark:bg-gray-600"></div>
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
            @click="$router.go(0)"
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
          >
            Réessayer
          </button>
        </div>

        <!-- Empty state -->
        <div
          v-else-if="filteredCommissions.length === 0"
          class="text-center py-12"
        >
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-users" class="h-8 w-8 text-gray-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Aucune commission trouvée
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Essayez avec d'autres mots-clés
          </p>
        </div>

        <!-- Grid des commissions -->
        <div v-else class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <article
            v-for="(commission, index) in filteredCommissions"
            :key="commission.id"
            class="commission-card group relative"
            :style="{ animationDelay: `${index * 100}ms` }"
          >
            <NuxtLink
              :to="`/assemblee-nationale/commissions/${commission.id}`"
              class="block h-full"
            >
              <div class="commission-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                <div class="p-6">
                  <!-- Header avec icône et numéro -->
                  <div class="mb-4 flex items-start gap-4">
                    <!-- Icône commission -->
                    <div class="flex h-12 w-12 flex-shrink-0 items-center justify-center rounded-xl bg-blue-100 transition-all duration-300 group-hover:scale-110 group-hover:bg-blue-200 dark:bg-blue-900/30 dark:group-hover:bg-blue-900/50">
                      <UIcon
                        name="i-heroicons-users"
                        class="h-6 w-6 text-blue-600 dark:text-blue-400"
                      />
                    </div>

                    <!-- Info commission -->
                    <div class="flex-1 min-w-0">
                      <div class="flex items-center gap-2 mb-1">
                        <span class="inline-flex items-center rounded-full bg-green-100 px-2 py-0.5 text-xs font-medium text-green-800 dark:bg-green-900/30 dark:text-green-400">
                          #{{ commission.id }}
                        </span>
                      </div>
                      <h3 class="text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400">
                        {{ commission.name }}
                      </h3>
                    </div>
                  </div>

                  <!-- Détails commission -->
                  <div class="space-y-3">
                    <!-- Président -->
                    <div v-if="commission.president" class="flex items-center text-sm text-gray-600 dark:text-gray-400">
                      <UIcon name="i-heroicons-user-circle" class="mr-2 h-4 w-4" />
                      <span class="font-medium">Président :</span>
                      <span class="ml-1">
                        {{ commission.president.first_name }} {{ commission.president.last_name }}
                      </span>
                    </div>

                    <!-- Nombre de membres -->
                    <div class="flex items-center text-sm text-gray-600 dark:text-gray-400">
                      <UIcon name="i-heroicons-users" class="mr-2 h-4 w-4" />
                      <span>{{ commission.members.length }} membre{{ commission.members.length > 1 ? 's' : '' }}</span>
                    </div>

                    <!-- Footer avec flèche -->
                    <div class="mt-4 flex items-center justify-between pt-2">
                      <div class="text-xs text-gray-500 dark:text-gray-400">
                        Voir les détails
                      </div>
                      <div class="flex h-7 w-7 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:scale-110 group-hover:bg-blue-50 dark:bg-gray-700 dark:group-hover:bg-blue-900/30">
                        <UIcon
                          name="i-heroicons-arrow-up-right"
                          class="h-3.5 w-3.5 text-gray-400 transition-colors duration-300 group-hover:text-blue-600 dark:group-hover:text-blue-400"
                        />
                      </div>
                    </div>
                  </div>
                </div>

                <!-- Effet de border animé -->
                <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20"></div>
              </div>
            </NuxtLink>
          </article>
        </div>
      </div>
    </div>
  </div>
</template>
