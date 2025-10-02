<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Rapports d'audit publics du Sénégal | Transparence et contrôle";
const description = "Consultez tous les rapports d'audit publics du Sénégal : OFNAC, Cour des Comptes, IGE, CENTIF, ARMP. Transparence et contrôle des finances publiques.";
const url = `${siteUrl}/documents/rapports-audit`;
const image = `${siteUrl}/images/rapports-audit-senegal.webp`;

const auditSchema = {
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
    description: "Rapports d'audit et de contrôle des institutions publiques",
    url: siteUrl,
  },
  mainEntity: {
    "@type": "ItemList",
    name: "Rapports d'audit publics",
    description: "Collection des rapports d'audit des institutions de contrôle du Sénégal",
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
      name: "Rapports d'audit",
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
    "rapports audit Sénégal",
    "OFNAC Sénégal",
    "Cour des Comptes Sénégal",
    "IGE Sénégal",
    "CENTIF Sénégal",
    "ARMP Sénégal",
    "transparence Sénégal",
    "contrôle finances publiques",
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
      children: JSON.stringify(auditSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

const { documents, loading, error } = useDocuments({ type: "audit_report" });

const searchQuery = ref("");
const selectedOrganisme = ref("");

const organismes = ["Cour des Comptes", "OFNAC", "CENTIF", "IGE", "ARMP"];

// Fonction pour obtenir l'icône de l'organisme
const getOrganismeIcon = (organisme: string) => {
  const icons = {
    "Cour des Comptes": "i-heroicons-building-office-2",
    "OFNAC": "i-heroicons-shield-check",
    "CENTIF": "i-heroicons-banknotes",
    "IGE": "i-heroicons-eye",
    "ARMP": "i-heroicons-clipboard-document-check",
  };
  return icons[organisme] || "i-heroicons-document-text";
};

const filteredRapports = computed(() =>
  documents.value.filter((rapport) => {
    return (
      (searchQuery.value.length === 0 ||
        rapport.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
        rapport.description
          .toLowerCase()
          .includes(searchQuery.value.toLowerCase())) &&
      (selectedOrganisme.value === "" ||
        rapport.audit_institution === selectedOrganisme.value)
    );
  }),
);

/* Pagination */

const page = ref(1);
const pageCount = 20;

const rowsfilteredRapports = computed(() => {
  return filteredRapports.value.slice(
    (page.value - 1) * pageCount,
    page.value * pageCount,
  );
});

// Réinitialiser la page lors du changement de type
watch(selectedOrganisme, () => {
  page.value = 1;
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
        <div class="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-full bg-red-100 dark:bg-red-900/30">
          <UIcon name="i-heroicons-document-magnifying-glass" class="h-8 w-8 text-red-600 dark:text-red-400" />
        </div>
        <h1
          class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white"
          itemprop="headline"
        >
          Rapports d'audit publics
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4 max-w-3xl mx-auto">
          Transparence et contrôle des finances publiques. Rapports des institutions de contrôle : OFNAC, Cour des Comptes, IGE, CENTIF, ARMP
        </p>
        <p v-if="documents.length" class="mt-4 text-sm font-medium text-gray-600 dark:text-gray-400">
          {{ documents.length }} rapports disponibles
        </p>
      </div>

      <!-- Section de recherche et filtres modernes -->
      <div class="mb-12">
        <div class="mx-auto max-w-4xl">
          <!-- Barre de recherche -->
          <div class="mb-6">
            <UInput
              v-model="searchQuery"
              size="lg"
              placeholder="Rechercher un rapport d'audit..."
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

          <!-- Filtres par organisme -->
          <div class="flex flex-wrap justify-center gap-3">
            <button
              @click="selectedOrganisme = ''"
              :class="[
                'inline-flex items-center rounded-full px-4 py-2 text-sm font-medium transition-all duration-200',
                selectedOrganisme === ''
                  ? 'bg-red-600 text-white shadow-md'
                  : 'bg-white text-gray-700 shadow-sm ring-1 ring-gray-200 hover:bg-gray-50 hover:shadow-md dark:bg-gray-800 dark:text-gray-300 dark:ring-gray-700 dark:hover:bg-gray-700'
              ]"
            >
              <UIcon name="i-heroicons-building-office" class="mr-2 h-4 w-4" />
              Tous
            </button>
            <button
              v-for="organisme in organismes"
              :key="organisme"
              @click="selectedOrganisme = selectedOrganisme === organisme ? '' : organisme"
              :class="[
                'inline-flex items-center rounded-full px-4 py-2 text-sm font-medium transition-all duration-200',
                selectedOrganisme === organisme
                  ? 'bg-red-600 text-white shadow-md'
                  : 'bg-white text-gray-700 shadow-sm ring-1 ring-gray-200 hover:bg-gray-50 hover:shadow-md dark:bg-gray-800 dark:text-gray-300 dark:ring-gray-700 dark:hover:bg-gray-700'
              ]"
            >
              <UIcon
                :name="getOrganismeIcon(organisme)"
                class="mr-2 h-4 w-4"
              />
              {{ organisme }}
            </button>
          </div>

          <div class="mt-4 text-center">
            <p class="text-sm text-gray-600 dark:text-gray-400">
              <span class="font-medium">{{ filteredRapports.length }}</span> rapports disponibles
              <span v-if="selectedOrganisme"> pour {{ selectedOrganisme }}</span>
            </p>
          </div>
        </div>
      </div>

      <!-- Loading state moderne -->
      <div v-if="loading" class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        <div
          v-for="n in 6"
          :key="n"
          class="audit-skeleton animate-pulse"
          :style="{ animationDelay: `${n * 100}ms` }"
        >
          <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="flex gap-4 p-6">
              <div class="flex-shrink-0">
                <div class="h-12 w-12 rounded-xl bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                  <div class="skeleton-shimmer h-full w-full rounded-xl"></div>
                </div>
              </div>
              <div class="flex-1 space-y-3">
                <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                <div class="h-4 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                <div class="flex items-center gap-2 mt-3">
                  <div class="h-4 w-4 rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-4 w-16 rounded bg-gray-200 dark:bg-gray-600"></div>
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
        v-else-if="!rowsfilteredRapports.length"
        class="text-center py-12"
      >
        <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
          <UIcon name="i-heroicons-document-magnifying-glass" class="h-8 w-8 text-gray-400" />
        </div>
        <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
          Aucun rapport d'audit
        </h3>
        <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
          <span v-if="selectedOrganisme">Aucun rapport disponible pour {{ selectedOrganisme }}</span>
          <span v-else>Les rapports d'audit seront affichés ici</span>
        </p>
      </div>

      <!-- Grid des rapports -->
      <div v-else class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        <article
          v-for="(rapport, index) in rowsfilteredRapports"
          :key="rapport.id"
          class="audit-report group relative"
          :style="{ animationDelay: `${index * 100}ms` }"
        >
          <NuxtLink
            :to="`/documents/${rapport.id}/${rapport.slug}`"
            class="block h-full"
          >
            <div class="audit-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
              <div class="flex gap-4 p-6">
                <!-- Logo de l'organisme -->
                <div class="flex-shrink-0">
                  <div class="relative h-12 w-12 overflow-hidden rounded-xl bg-gradient-to-br from-red-50 to-red-100 dark:from-red-900/20 dark:to-red-800/20">
                    <img
                      v-if="rapport.audit_institution == 'ARMP'"
                      src="~/assets/logos/armp.webp"
                      loading="lazy"
                      alt="Logo ARMP"
                      class="h-full w-full object-contain p-1 transition-transform duration-500 group-hover:scale-105"
                    />
                    <img
                      v-else-if="rapport.audit_institution == 'OFNAC'"
                      src="~/assets/logos/ofnac.webp"
                      loading="lazy"
                      alt="Logo OFNAC"
                      class="h-full w-full object-contain p-1 transition-transform duration-500 group-hover:scale-105"
                    />
                    <img
                      v-else-if="rapport.audit_institution == 'IGE'"
                      src="~/assets/logos/ige.webp"
                      loading="lazy"
                      alt="Logo IGE"
                      class="h-full w-full object-contain p-1 transition-transform duration-500 group-hover:scale-105"
                    />
                    <img
                      v-else-if="rapport.audit_institution == 'Cour des Comptes'"
                      src="~/assets/logos/cour_des_comptes.webp"
                      loading="lazy"
                      alt="Logo Cour des Comptes"
                      class="h-full w-full object-contain p-1 transition-transform duration-500 group-hover:scale-105"
                    />
                    <img
                      v-else-if="rapport.audit_institution == 'CENTIF'"
                      src="~/assets/logos/centif.webp"
                      loading="lazy"
                      alt="Logo CENTIF"
                      class="h-full w-full object-contain p-1 transition-transform duration-500 group-hover:scale-105"
                    />
                    <div
                      v-else
                      class="flex h-full items-center justify-center"
                    >
                      <UIcon name="i-heroicons-document-magnifying-glass" class="h-6 w-6 text-red-500/60" />
                    </div>
                  </div>
                </div>

                <!-- Contenu -->
                <div class="flex-1 min-w-0">
                  <!-- Badge organisme -->
                  <div class="mb-2">
                    <span class="inline-flex items-center rounded-full bg-red-100 px-2.5 py-0.5 text-xs font-medium text-red-800 dark:bg-red-900/30 dark:text-red-400">
                      {{ rapport.audit_institution || 'Autre' }}
                    </span>
                  </div>

                  <!-- Titre -->
                  <h3 class="mb-3 text-sm font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-red-600 dark:text-white dark:group-hover:text-red-400 line-clamp-3">
                    {{ rapport.title }}
                  </h3>

                  <!-- Footer -->
                  <div class="flex items-center justify-between">
                    <div class="flex items-center text-xs text-gray-500 dark:text-gray-400">
                      <UIcon name="i-heroicons-document-text" class="mr-1 h-3 w-3" />
                      <span>Rapport d'audit</span>
                    </div>

                    <!-- Flèche de navigation -->
                    <div class="flex h-6 w-6 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:scale-110 group-hover:bg-red-50 dark:bg-gray-700 dark:group-hover:bg-red-900/30">
                      <UIcon
                        name="i-heroicons-arrow-up-right"
                        class="h-3 w-3 text-gray-400 transition-colors duration-300 group-hover:text-red-600 dark:group-hover:text-red-400"
                      />
                    </div>
                  </div>
                </div>
              </div>

              <!-- Effet de border animé -->
              <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-red-500/20 dark:ring-white/10 dark:group-hover:ring-red-400/20"></div>
            </div>
          </NuxtLink>
        </article>
      </div>

      <!-- Pagination moderne -->
      <div
        v-if="filteredRapports.length > pageCount"
        class="mt-12 flex justify-center"
      >
        <UPagination
          v-model="page"
          size="md"
          :page-count="pageCount"
          :total="filteredRapports.length"
          :active-button="{ color: 'red' }"
          :ui="{
            wrapper: 'flex items-center gap-1',
            base: 'min-w-9 min-h-9 flex items-center justify-center rounded-lg focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 disabled:opacity-50 disabled:pointer-events-none disabled:cursor-not-allowed transition-colors',
            active: 'bg-red-600 text-white shadow-sm',
            inactive: 'bg-white text-gray-700 hover:bg-gray-50 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700 border border-gray-300 dark:border-gray-600',
          }"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Animation d'apparition des rapports */
@keyframes auditReportFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.audit-report {
  animation: auditReportFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.audit-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .audit-card-inner {
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
.audit-skeleton {
  animation: auditReportFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Line clamp pour les titres longs */
.line-clamp-3 {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  line-height: 1.4;
  max-height: 4.2em;
}

/* États focus pour l'accessibilité */
.audit-report a:focus-visible {
  outline: 2px solid #dc2626;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .audit-card-inner {
    border-radius: 1rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .audit-report {
    animation: none;
    opacity: 1;
  }

  .group:hover .audit-card-inner {
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
.audit-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .audit-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .audit-report {
    break-inside: avoid;
  }

  .audit-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .audit-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}
</style>

<style scoped>
.scrollable-hidden {
  overflow-x: auto;
  /* Masque la barre sur Firefox */
  scrollbar-width: none;
  /* Masque la barre sur Internet Explorer et Edge */
  -ms-overflow-style: none;
}

.scrollable-hidden::-webkit-scrollbar {
  /* Masque la barre sur Chrome, Safari et Opera */
  display: none;
}
</style>
