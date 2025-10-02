<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Bureau de l'Assemblée nationale du Sénégal | 15e législature";
const description = "Découvrez la composition du bureau de l'Assemblée nationale du Sénégal : président, vice-présidents, secrétaires et questeurs de la 15e législature.";
const url = `${siteUrl}/assemblee-nationale/bureau`;
const image = `${siteUrl}/images/bureau-assemblee-senegal.webp`;

const bureauPageSchema = {
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
  about: {
    "@type": "GovernmentOrganization",
    name: "Bureau de l'Assemblée nationale du Sénégal",
    description: "Organe dirigeant de l'Assemblée nationale",
    url: url,
  },
  mainEntity: {
    "@type": "Organization",
    name: "Bureau de l'Assemblée nationale",
    description: "Direction collégiale de l'Assemblée nationale du Sénégal",
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
      name: "Bureau",
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
    "bureau Assemblée nationale Sénégal",
    "président Assemblée Sénégal",
    "vice-présidents Assemblée",
    "secrétaires Assemblée",
    "questeurs Assemblée",
    "15e législature bureau",
    "direction Assemblée nationale",
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
      children: JSON.stringify(bureauPageSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
  ],
});

const { office, loading, error } = useAssemblyOffice();

interface OfficeGroup {
  name: string;
  members: any[];
  columns: string;
  count: number; // Nombre de skeletons à afficher
}

// Configuration des groupes pour le skeleton et l'affichage réel
const groupsConfig = [
  {
    name: "Président",
    role: "president",
    columns: "max-w-xs mx-auto",
    count: 1,
  },
  {
    name: "Vice-présidents",
    role: "vice_president",
    columns: "grid-cols-2 md:grid-cols-4",
    count: 8,
  },
  {
    name: "Secrétaires",
    role: "secretary",
    columns: "grid-cols-2 md:grid-cols-3",
    count: 6,
  },
  {
    name: "Questeurs",
    role: "quaestor",
    columns: "grid-cols-2 max-w-2xl mx-auto",
    count: 2,
  },
];

const groupedMembers = computed<OfficeGroup[]>(() => {
  if (!office.value) return [];

  const groups: OfficeGroup[] = [];

  groupsConfig.forEach((config) => {
    const members =
      config.role === "president"
        ? office.value.find((member) => member.role === config.role)?.deputy
          ? [office.value.find((member) => member.role === config.role)!.deputy]
          : []
        : office.value
            .filter((member) => member.role === config.role)
            .sort((a, b) => (a.rank || 0) - (b.rank || 0))
            .map((member) => member.deputy);

    if (members.length) {
      groups.push({
        name: config.name,
        members,
        columns: config.columns,
        count: config.count,
      });
    }
  });

  return groups;
});
</script>

<style scoped>
/* Animation d'apparition des sections */
@keyframes bureauSectionFadeIn {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.bureau-section {
  animation: bureauSectionFadeIn 0.8s ease-out forwards;
  opacity: 0;
}

/* Animation pour les cartes individuelles */
@keyframes deputyCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.deputy-wrapper {
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

/* Skeleton loading animation */
.bureau-skeleton {
  animation: deputyCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .bureau-container {
    padding: 0 0.5rem;
  }

  .bureau-section {
    margin-bottom: 2rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .bureau-section,
  .deputy-wrapper {
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
.bureau-container {
  contain: layout style paint;
}

/* Print styles */
@media print {
  .bureau-section {
    break-inside: avoid;
  }

  .deputy-wrapper {
    break-inside: avoid;
  }
}

/* Dark mode enhancements */
.dark .bureau-container {
  color-scheme: dark;
}

/* Effet spécial pour le président */
.bureau-section:first-child .deputy-wrapper {
  position: relative;
}

.bureau-section:first-child .deputy-wrapper::after {
  content: '';
  position: absolute;
  inset: -4px;
  border-radius: 1.5rem;
  padding: 4px;
  background: linear-gradient(45deg, #3b82f6, #1d4ed8, #3b82f6);
  background-size: 200% 200%;
  animation: gradientShift 3s ease infinite;
  z-index: -1;
  opacity: 0.1;
}

@keyframes gradientShift {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

/* Effet de focus amélioré */
.deputy-wrapper:focus-within {
  transform: scale(1.02);
  transition: transform 0.2s ease-out;
}
</style>

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
      <div class="text-center mb-12">
        <h1
          class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white"
          itemprop="headline"
        >
          Bureau de l'Assemblée nationale
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4 max-w-3xl mx-auto">
          Direction collégiale de l'Assemblée nationale. Le bureau assure la bonne marche des travaux parlementaires et l'administration de l'institution
        </p>
      </div>

      <!-- Content section -->
      <div class="bureau-container">
        <!-- Loading state moderne -->
        <div v-if="loading" class="space-y-12">
          <section v-for="group in groupsConfig" :key="group.name">
            <div class="text-center mb-8">
              <div class="h-8 w-48 mx-auto rounded bg-gray-200 dark:bg-gray-700 animate-pulse"></div>
            </div>
            <div :class="['grid gap-6', group.columns]">
              <div
                v-for="n in group.count"
                :key="n"
                class="bureau-skeleton animate-pulse"
                :style="{ animationDelay: `${n * 100}ms` }"
              >
                <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
                  <div class="p-6">
                    <!-- Photo skeleton -->
                    <div class="mx-auto mb-4 h-24 w-24 rounded-full bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                      <div class="skeleton-shimmer h-full w-full rounded-full"></div>
                    </div>
                    <!-- Nom skeleton -->
                    <div class="space-y-2 text-center">
                      <div class="h-5 w-3/4 mx-auto rounded bg-gray-200 dark:bg-gray-600"></div>
                      <div class="h-4 w-1/2 mx-auto rounded bg-gray-200 dark:bg-gray-600"></div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </section>
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

        <!-- Content avec animation séquentielle -->
        <div v-else class="space-y-16">
          <section
            v-for="(group, groupIndex) in groupedMembers"
            :key="group.name"
            class="bureau-section"
            :style="{ animationDelay: `${groupIndex * 200}ms` }"
          >
            <!-- Titre de section avec style différentié -->
            <div class="text-center mb-8">
              <h2 class="text-xl font-bold text-gray-900 dark:text-white sm:text-2xl">
                {{ group.name }}
              </h2>
              <div class="mt-2 mx-auto h-1 w-16 rounded-full bg-gradient-to-r from-blue-500 to-blue-600"></div>
            </div>

            <!-- Grid adaptée avec wrapper d'animation -->
            <div :class="['grid gap-6', group.columns]">
              <div
                v-for="(deputy, index) in group.members"
                :key="deputy.id"
                class="deputy-wrapper"
                :style="{ animationDelay: `${(groupIndex * 200) + (index * 100)}ms` }"
              >
                <AssemblyDeputyCard :deputy="deputy" />
              </div>
            </div>
          </section>
        </div>
      </div>
    </div>
  </div>
</template>
