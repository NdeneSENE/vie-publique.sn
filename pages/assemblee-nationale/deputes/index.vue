<template>
  <div class="py-6 sm:py-8">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8" itemscope itemtype="https://schema.org/WebPage">
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
          Annuaire des députés
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4">
          Les 165 députés élus de la 15e législature de l'Assemblée nationale
        </p>
      </div>

      <!-- Composant modernisé avec states de chargement -->
      <div class="deputies-container">
        <!-- Loading state moderne -->
        <div v-if="loading" class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4">
          <div
            v-for="n in 12"
            :key="n"
            class="deputy-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 80}ms` }"
          >
            <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <!-- Photo skeleton -->
              <div class="aspect-square bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                <div class="skeleton-shimmer h-full w-full"></div>
              </div>

              <!-- Content skeleton -->
              <div class="p-4">
                <div class="space-y-2">
                  <div class="h-4 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-3 w-1/2 rounded bg-gray-200 dark:bg-gray-600"></div>
                </div>
                <div class="mt-3 space-y-2">
                  <div class="h-3 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-3 w-2/3 rounded bg-gray-200 dark:bg-gray-600"></div>
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
            @click="fetchElectedDeputies()"
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
          >
            Réessayer
          </button>
        </div>

        <!-- Grid des députés -->
        <ElectionResultDeputiesGrid2
          v-else
          :deputies="deputies"
          :loading="loading"
          :error="error"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useDeputev2 } from "@/composables/parliament/useDeputev2";

const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();

const title = "Députés de l'Assemblée Nationale du Sénégal | 15e législature";
const description =
  "Retrouvez tous les 165 députés en activité de l'Assemblée nationale du Sénégal. Liste complète de la 15e législature avec résultats de vote et analyses.";
const url = `${siteUrl}/assemblee-nationale/deputes`;
const image = `${siteUrl}/images/vpsn-share-elections.png`;

const deputiesSchema = {
  "@context": "https://schema.org",
  "@type": "WebPage",
  name: title,
  description: description,
  url: url,
  image: image,
  isPartOf: {
    "@type": "WebSite",
    name: siteName,
    url: siteUrl,
  },
  about: [
    {
      "@type": "GovernmentOrganization",
      name: "Assemblée nationale du Sénégal",
      description: "Parlement du Sénégal",
    },
    {
      "@type": "Thing",
      name: "15e législature du Sénégal",
    },
  ],
  mainEntity: {
    "@type": "ItemList",
    name: "Députés de la 15e législature",
    description:
      "Liste des 165 députés élus de l'Assemblée nationale du Sénégal",
    numberOfItems: 165,
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
      name: "Députés",
      item: url,
    },
  ],
};

const organizationSchema = {
  "@context": "https://schema.org",
  "@type": "LegislativeBuilding",
  name: "Assemblée nationale du Sénégal",
  url: `${siteUrl}/assemblee-nationale`,
  description: "Parlement unicaméral de la République du Sénégal",
  address: {
    "@type": "PostalAddress",
    streetAddress: "Avenue Léopold Sédar Senghor",
    addressLocality: "Dakar",
    addressCountry: "SN",
  },
  governmentType: "Legislature",
  numberOfMembers: 165,
  politicalSystem: "Démocratie parlementaire",
  foundingDate: "1960",
  legislativeTerm: "15e législature",
};

const governmentSchema = {
  "@context": "https://schema.org",
  "@type": "GovernmentOrganization",
  name: "Assemblée nationale du Sénégal",
  url: url,
  description:
    "Institution législative de la République du Sénégal composée de 165 députés",
  address: {
    "@type": "PostalAddress",
    addressCountry: "SN",
    addressLocality: "Dakar",
  },
  areaServed: {
    "@type": "Country",
    name: "Sénégal",
  },
  parentOrganization: {
    "@type": "GovernmentOrganization",
    name: "République du Sénégal",
  },
};

// SEO Meta Tags
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
    "députés Sénégal",
    "Assemblée nationale Sénégal",
    "15e législature",
    "parlement sénégalais",
    "élus nationaux Sénégal",
    "représentants peuple sénégalais",
    "parlementaires Sénégal",
    "législateurs Sénégal",
  ].join(", "),
});

// Head Configuration
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
      children: JSON.stringify(deputiesSchema),
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
      children: JSON.stringify(governmentSchema),
    },
  ],
});

const { deputies, loading, error, fetchElectedDeputies } = useDeputev2();

// Chargement des données au montage
onMounted(async () => {
  await fetchElectedDeputies();
});
</script>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes deputyCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.deputy-skeleton {
  animation: deputyCardFadeIn 0.6s ease-out forwards;
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

/* Responsive améliorations */
@media (max-width: 640px) {
  .deputies-container {
    padding: 0 0.5rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .deputy-skeleton {
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
.deputies-container {
  contain: layout style paint;
}

/* Print styles */
@media print {
  .deputy-skeleton {
    break-inside: avoid;
  }
}

/* Dark mode enhancements */
.dark .deputies-container {
  color-scheme: dark;
}
</style>
