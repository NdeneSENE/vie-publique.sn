<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Annuaire des Sites Web Publics du Sénégal";
const description = "Découvrez l'annuaire complet des sites internet publics du Sénégal. Plus de 50 sites officiels d'institutions, ministères et services publics sénégalais.";
const url = `${siteUrl}/annuaire-sites-publics-senegal`;

const websiteSchema = {
  "@context": "https://schema.org",
  "@type": "WebPage",
  "name": title,
  "description": description,
  "url": url,
  "isPartOf": {
    "@type": "WebSite",
    "name": siteName,
    "url": siteUrl,
  },
  "about": {
    "@type": "Thing",
    "name": "Sites web publics Sénégal",
    "description": "Annuaire des sites internet officiels du secteur public sénégalais",
  },
  "mainEntity": {
    "@type": "ItemList",
    "name": "Sites Web Publics Sénégal",
    "description": "Liste des sites internet publics du Sénégal",
  },
};

const breadcrumbSchema = {
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "Accueil",
      "item": siteUrl,
    },
    {
      "@type": "ListItem",
      "position": 2,
      "name": "Annuaires",
      "item": `${siteUrl}/annuaires`,
    },
    {
      "@type": "ListItem",
      "position": 3,
      "name": "Sites Web Publics",
      "item": url,
    },
  ],
};

useSeoMeta({
  title,
  ogTitle: title,
  description,
  ogDescription: description,
  ogImage: defaultImage,
  ogUrl: url,
  twitterCard: "summary_large_image",
  twitterTitle: title,
  twitterDescription: description,
  twitterImage: defaultImage,
  keywords: [
    ...keywords,
    "sites web publics Sénégal",
    "annuaire sites internet",
    "institutions publiques en ligne",
    "services numériques Sénégal",
    "portails gouvernementaux",
  ].join(", "),
});

useHead({
  htmlAttrs: { lang: "fr-SN" },
  link: [{ rel: "canonical", href: url }],
  meta: [
    { name: "theme-color", content: themeColor },
    { name: "author", content: siteName },
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
      children: JSON.stringify(websiteSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

/* Get Datas */

const nuxtApp = useNuxtApp();
const { data, error } = await useFetch("/api/websites", {
  watch: false,

  transform(input) {
    return {
      sites: input,
      fetchedAt: new Date(),
    };
  },

  getCachedData(key) {
    const data = nuxtApp.payload.data[key] || nuxtApp.static.data[key];
    if (!data) {
      return;
    }
    const expirationDate = new Date(data.fetchedAt);
    expirationDate.setTime(expirationDate.getTime() + 120 * 1000); // 120 secondes
    const isExpired = expirationDate.getTime() < Date.now();
    if (isExpired) {
      return;
    }

    return data;
  },
});

if (error.value) {
  console.error("Failed to fetch websites data:", error.value);
}

/* State */
const isLoading = ref(false);
const searchQuery = ref("");
const selectedType = ref("");

/* Filters and Computed */
const filteredSites = computed(() => {
  if (!data.value?.sites) return [];
  return data.value.sites.filter(
    (site: any) =>
      (site.nom.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
        site.url.toLowerCase().includes(searchQuery.value.toLowerCase())) &&
      (selectedType.value ? site.type === selectedType.value : true),
  );
});

const types = computed(() => {
  if (!data.value?.sites) return [];
  const allTypes = data.value.sites.map((site: any) => site.type.trim());
  return [
    { label: "Tous les types", value: "" },
    ...Array.from(new Set(allTypes)).map(type => ({ label: type, value: type }))
  ];
});

const totalSites = computed(() => data.value?.sites?.length || 0);
const filteredCount = computed(() => filteredSites.value.length);

const sitesByType = computed(() => {
  if (!data.value?.sites) return {};
  return data.value.sites.reduce((acc: any, site: any) => {
    const type = site.type.trim();
    acc[type] = (acc[type] || 0) + 1;
    return acc;
  }, {});
});

/* Pagination */

const page = ref(1);
const pageCount = 20;

const rowsFilteredSites = computed(() => {
  return filteredSites.value.slice(
    (page.value - 1) * pageCount,
    page.value * pageCount,
  );
});

// Réinitialiser la page lors du changement de filtres
watch([selectedType, searchQuery], () => {
  page.value = 1;
});

// Fonction pour obtenir l'icône selon le type de site
const getTypeIcon = (type: string) => {
  const typeMap: Record<string, string> = {
    'Ministère': 'i-heroicons-building-office',
    'Institution': 'i-heroicons-building-library',
    'Service Public': 'i-heroicons-identification',
    'Agence': 'i-heroicons-briefcase',
    'Université': 'i-heroicons-academic-cap',
    'Collectivité': 'i-heroicons-map',
    'default': 'i-heroicons-computer-desktop'
  };
  return typeMap[type] || typeMap.default;
};

// Fonction pour obtenir la couleur selon le type
const getTypeColor = (type: string) => {
  const colorMap: Record<string, string> = {
    'Ministère': 'blue',
    'Institution': 'purple',
    'Service Public': 'green',
    'Agence': 'amber',
    'Université': 'indigo',
    'Collectivité': 'red',
    'default': 'gray'
  };
  return colorMap[type] || colorMap.default;
};

// Animation delay pour les cartes
const getAnimationDelay = (index: number) => `${index * 50}ms`;
</script>

<template>
  <div class="min-h-screen bg-gray-50 dark:bg-gray-900">
    <!-- Breadcrumb moderne -->
    <nav class="border-b border-gray-200 bg-white dark:border-gray-700 dark:bg-gray-800" aria-label="Breadcrumb">
      <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <div class="flex h-16 items-center space-x-4">
          <ol class="flex items-center space-x-2">
            <li>
              <NuxtLink to="/" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
                <UIcon name="i-heroicons-home" class="h-5 w-5" />
                <span class="sr-only">Accueil</span>
              </NuxtLink>
            </li>
            <UIcon name="i-heroicons-chevron-right" class="h-4 w-4 text-gray-300 dark:text-gray-600" />
            <li>
              <NuxtLink to="/annuaires" class="text-sm font-medium text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                Annuaires
              </NuxtLink>
            </li>
            <UIcon name="i-heroicons-chevron-right" class="h-4 w-4 text-gray-300 dark:text-gray-600" />
            <li>
              <span class="text-sm font-medium text-blue-600 dark:text-blue-400" aria-current="page">
                Sites Web Publics
              </span>
            </li>
          </ol>
        </div>
      </div>
    </nav>

    <div class="mx-auto max-w-7xl px-4 py-8 sm:px-6 lg:px-8">
      <!-- Header Section -->
      <div class="text-center">
        <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-blue-100 dark:bg-blue-900/50">
          <UIcon name="i-heroicons-computer-desktop" class="h-8 w-8 text-blue-600 dark:text-blue-400" />
        </div>
        <h1 class="mt-4 text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl">
          Sites Web Publics du Sénégal
        </h1>
        <p class="mx-auto mt-4 max-w-2xl text-lg text-gray-600 dark:text-gray-300">
          Découvrez l'annuaire complet des {{ totalSites }} sites internet officiels des institutions et services publics sénégalais
        </p>
      </div>

      <!-- Filtres et Recherche -->
      <div class="mt-8">
        <div class="grid gap-4 md:grid-cols-2">
          <div class="relative">
            <UInput
              v-model="searchQuery"
              size="lg"
              icon="i-heroicons-magnifying-glass"
              placeholder="Rechercher un site web..."
              class="w-full"
              :loading="isLoading"
            />
          </div>
          <div class="relative">
            <USelect
              v-model="selectedType"
              size="lg"
              icon="i-heroicons-funnel"
              placeholder="Filtrer par type d'institution"
              :options="types"
              option-attribute="label"
              value-attribute="value"
              class="w-full"
            />
          </div>
        </div>

        <!-- Résultats et stats -->
        <div class="mt-4 flex items-center justify-between">
          <p class="text-sm text-gray-600 dark:text-gray-400">
            <span class="font-medium text-gray-900 dark:text-white">{{ filteredCount }}</span>
            {{ filteredCount === 1 ? 'site trouvé' : 'sites trouvés' }}
            <span v-if="filteredCount !== totalSites"> sur {{ totalSites }}</span>
          </p>
          <UButton
            v-if="searchQuery || selectedType"
            variant="ghost"
            size="sm"
            icon="i-heroicons-x-mark"
            @click="searchQuery = ''; selectedType = ''"
          >
            Effacer les filtres
          </UButton>
        </div>
      </div>

      <!-- Grille des sites -->
      <div class="mt-8">
        <!-- État de chargement -->
        <div v-if="isLoading" class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div v-for="i in 6" :key="i" class="animate-pulse">
            <div class="rounded-xl bg-gray-200 p-6 dark:bg-gray-700">
              <div class="mb-4 h-4 w-3/4 rounded bg-gray-300 dark:bg-gray-600"></div>
              <div class="mb-2 h-3 w-full rounded bg-gray-300 dark:bg-gray-600"></div>
              <div class="h-3 w-1/2 rounded bg-gray-300 dark:bg-gray-600"></div>
            </div>
          </div>
        </div>

        <!-- Sites Web -->
        <div v-else-if="filteredSites.length > 0" class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div
            v-for="(site, index) in rowsFilteredSites"
            :key="site.url"
            class="group site-card"
            :style="{ animationDelay: getAnimationDelay(index) }"
          >
            <div class="relative h-full overflow-hidden rounded-xl border border-gray-200 bg-white p-6 shadow-sm transition-all duration-300 hover:shadow-lg hover:-translate-y-1 dark:border-gray-700 dark:bg-gray-800">
              <!-- Badge de type -->
              <div class="absolute right-4 top-4">
                <UBadge
                  :color="getTypeColor(site.type)"
                  variant="subtle"
                  size="xs"
                >
                  {{ site.type }}
                </UBadge>
              </div>

              <!-- Icône -->
              <div class="mb-4">
                <div class="flex h-12 w-12 items-center justify-center rounded-lg bg-blue-100 dark:bg-blue-900/50">
                  <UIcon
                    :name="getTypeIcon(site.type)"
                    class="h-6 w-6 text-blue-600 dark:text-blue-400"
                  />
                </div>
              </div>

              <!-- Contenu -->
              <div class="space-y-3">
                <h3 class="text-lg font-semibold text-gray-900 dark:text-white line-clamp-2">
                  {{ site.nom }}
                </h3>

                <ULink
                  :to="site.url"
                  target="_blank"
                  external
                  class="inline-flex items-center text-sm font-medium text-blue-600 hover:text-blue-700 dark:text-blue-400 dark:hover:text-blue-300"
                >
                  <span class="truncate">{{ site.url.replace(/^https?:\/\//, '').replace(/\/$/, '') }}</span>
                  <UIcon name="i-heroicons-arrow-top-right-on-square" class="ml-1 h-4 w-4 flex-shrink-0" />
                </ULink>
              </div>

              <!-- Effet de hover -->
              <div class="absolute inset-0 rounded-xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-gray-900/10 dark:ring-white/10 dark:group-hover:ring-white/20"></div>
            </div>
          </div>
        </div>

        <!-- État vide -->
        <div v-else class="text-center py-12">
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-magnifying-glass" class="h-8 w-8 text-gray-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Aucun site trouvé
          </h3>
          <p class="mt-2 text-gray-600 dark:text-gray-400">
            Essayez de modifier vos critères de recherche.
          </p>
        </div>
      </div>

      <!-- Pagination -->
      <div v-if="filteredSites.length > pageCount" class="mt-8 flex justify-center">
        <UPagination
          v-model="page"
          :page-count="pageCount"
          :total="filteredSites.length"
          :show-last="true"
          :show-first="true"
        />
      </div>

      <!-- Statistiques par type -->
      <div v-if="Object.keys(sitesByType).length > 0" class="mt-12">
        <div class="rounded-xl bg-white p-6 shadow-sm ring-1 ring-gray-200/60 dark:bg-gray-800 dark:ring-gray-700/60">
          <h2 class="text-lg font-semibold text-gray-900 dark:text-white mb-4">
            Répartition par type d'institution
          </h2>
          <div class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
            <div
              v-for="(count, type) in sitesByType"
              :key="type"
              class="flex items-center justify-between rounded-lg bg-gray-50 p-3 dark:bg-gray-700/50"
            >
              <div class="flex items-center space-x-3">
                <div class="flex h-8 w-8 items-center justify-center rounded-lg bg-blue-100 dark:bg-blue-900/50">
                  <UIcon
                    :name="getTypeIcon(type)"
                    class="h-4 w-4 text-blue-600 dark:text-blue-400"
                  />
                </div>
                <span class="text-sm font-medium text-gray-900 dark:text-white">{{ type }}</span>
              </div>
              <UBadge :color="getTypeColor(type)" variant="subtle">
                {{ count }}
              </UBadge>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

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

.site-card {
  animation: fadeInUp 0.6s ease-out forwards;
  opacity: 0;
}

/* Optimisations performance */
.site-card {
  contain: layout style paint;
  will-change: transform;
}

.group:hover .site-card {
  will-change: auto;
}

/* États focus pour l'accessibilité */
.group:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 0.75rem;
}

/* Réduction d'animation pour l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .site-card {
    animation: none;
    opacity: 1;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .site-card {
    margin-bottom: 0.5rem;
  }
}

/* Support pour line-clamp */
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
