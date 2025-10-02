<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();

const title =
  "Questions écrites à l'Assemblée nationale du Sénégal | 15e législature";
const description =
  "Consultez toutes les questions écrites posées par les députés de la 15e législature de l'Assemblée nationale du Sénégal. Activité parlementaire et contrôle de l'action gouvernementale.";
const url = `${siteUrl}/assemblee-nationale/questions`;
const image = `${siteUrl}/images/questions-ecrites-assemblee.webp`;

const questionsCollectionSchema = {
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
    name: "Questions écrites parlementaires",
    description:
      "Collection des questions écrites posées par les députés sénégalais",
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
      name: "Questions écrites",
      item: url,
    },
  ],
};

const organizationSchema = {
  "@context": "https://schema.org",
  "@type": "LegislativeBuilding",
  name: "Assemblée nationale du Sénégal",
  url: `${siteUrl}/assemblee-nationale`,
  description: "Institution législative de la République du Sénégal",
  address: {
    "@type": "PostalAddress",
    streetAddress: "Avenue Léopold Sédar Senghor",
    addressLocality: "Dakar",
    addressCountry: "SN",
  },
  governmentType: "Legislature",
  numberOfMembers: 165,
  legislativeTerm: "15e législature",
};

const governmentServiceSchema = {
  "@context": "https://schema.org",
  "@type": "GovernmentService",
  name: "Questions écrites parlementaires",
  description:
    "Service de questions écrites permettant aux députés d'interroger le gouvernement",
  provider: {
    "@type": "GovernmentOrganization",
    name: "Assemblée nationale du Sénégal",
  },
  areaServed: {
    "@type": "Country",
    name: "Sénégal",
  },
  serviceType: "Contrôle parlementaire",
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
    "questions écrites Assemblée nationale",
    "députés sénégalais questions",
    "contrôle parlementaire Sénégal",
    "15e législature questions",
    "activité parlementaire Sénégal",
    "questions gouvernement Sénégal",
    "parlement sénégalais contrôle",
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
      children: JSON.stringify(questionsCollectionSchema),
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

const config = useRuntimeConfig();
const { questions, loading, error } = useAssemblyQuestions();

const getImageUrl = (imageId: string) => {
  return `${config.public.cmsApiUrl}/assets/${imageId}`;
};

// Calcul des statistiques
const topDeputies = computed(() => {
  if (!questions.value) return [];

  // Grouper les questions par député
  const questionsByDeputy = questions.value.reduce((acc, question) => {
    const deputyId = question.deputy.id;
    if (!acc[deputyId]) {
      acc[deputyId] = {
        id: deputyId,
        first_name: question.deputy.first_name,
        last_name: question.deputy.last_name,
        photo: question.deputy.photo,
        questionsCount: 0,
      };
    }
    acc[deputyId].questionsCount++;
    return acc;
  }, {});

  // Convertir en tableau et trier
  return Object.values(questionsByDeputy)
    .sort((a, b) => b.questionsCount - a.questionsCount)
    .slice(0, 4);
});

// Pagination
const itemsPerPage = ref(10);
const currentPage = ref(1);
// Questions paginées
const paginatedQuestions = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return questions.value.slice(start, end);
});

const handlePageChange = (page: number) => {
  currentPage.value = page;
  // Faire défiler vers le haut de la liste
  window.scrollTo({ top: 0, behavior: "smooth" });
};

const formatDateISO = (date: string) => {
  return new Date(date).toISOString();
};
</script>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes questionCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.question-card,
.deputy-card {
  animation: questionCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.question-card-inner,
.deputy-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .question-card-inner,
.group:hover .deputy-card-inner {
  transform: translateY(-2px);
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
.question-skeleton,
.deputy-skeleton {
  animation: questionCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Line clamp pour les sujets */
.line-clamp-2 {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  line-height: 1.5;
  max-height: 3em;
}

/* États focus pour l'accessibilité */
.question-card a:focus-visible,
.deputy-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .question-card-inner,
  .deputy-card-inner {
    border-radius: 1rem;
  }

  .question-card-inner .p-6,
  .deputy-card-inner .p-6 {
    padding: 1.25rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .question-card,
  .deputy-card {
    animation: none;
    opacity: 1;
  }

  .skeleton-shimmer {
    animation: none;
  }

  .group:hover .question-card-inner,
  .group:hover .deputy-card-inner {
    transform: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.question-card-inner,
.deputy-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .question-card-inner,
.group:hover .deputy-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .question-card,
  .deputy-card {
    break-inside: avoid;
  }

  .question-card-inner,
  .deputy-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .question-card-inner,
.dark .deputy-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

/* Glow effect pour les badges */
.group:hover [class*="bg-yellow-"] {
  box-shadow: 0 0 20px rgba(245, 158, 11, 0.3);
}

.group:hover [class*="bg-blue-"] {
  box-shadow: 0 0 20px rgba(59, 130, 246, 0.3);
}

.group:hover [class*="bg-amber-"] {
  box-shadow: 0 0 20px rgba(245, 158, 11, 0.3);
}

.group:hover [class*="bg-slate-"] {
  box-shadow: 0 0 20px rgba(100, 116, 139, 0.3);
}
</style>

<template>
  <div class="py-6 sm:py-8">
    <div
      class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8"
      itemscope
      itemtype="https://schema.org/CollectionPage"
    >
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
          Questions écrites
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4">
          Activité parlementaire et contrôle de l'action gouvernementale
        </p>
      </div>

      <!-- Content section -->
      <div class="questions-container">
        <!-- Loading state moderne -->
        <div v-if="loading" class="space-y-8">
          <!-- Skeleton pour top députés -->
          <div>
            <div class="h-6 w-48 rounded bg-gray-200 dark:bg-gray-700 mb-4 animate-pulse"></div>
            <div class="grid grid-cols-2 gap-4 md:grid-cols-4">
              <div
                v-for="n in 4"
                :key="n"
                class="deputy-skeleton animate-pulse"
                :style="{ animationDelay: `${n * 100}ms` }"
              >
                <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700 p-4">
                  <div class="flex flex-col items-center space-y-3">
                    <div class="h-20 w-20 rounded-full bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                      <div class="skeleton-shimmer h-full w-full rounded-full"></div>
                    </div>
                    <div class="space-y-2 text-center w-full">
                      <div class="h-4 w-3/4 mx-auto rounded bg-gray-200 dark:bg-gray-600"></div>
                      <div class="h-3 w-1/2 mx-auto rounded bg-gray-200 dark:bg-gray-600"></div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Skeleton pour questions -->
          <div class="space-y-4">
            <div class="h-6 w-32 rounded bg-gray-200 dark:bg-gray-700 animate-pulse"></div>
            <div
              v-for="n in 6"
              :key="n"
              class="question-skeleton animate-pulse"
              :style="{ animationDelay: `${n * 80}ms` }"
            >
              <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700 p-6">
                <div class="flex gap-4">
                  <div class="h-20 w-20 rounded-full bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700 flex-shrink-0">
                    <div class="skeleton-shimmer h-full w-full rounded-full"></div>
                  </div>
                  <div class="flex-1 space-y-3">
                    <div class="h-3 w-24 rounded bg-gray-200 dark:bg-gray-600"></div>
                    <div class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                    <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                    <div class="h-4 w-32 rounded bg-gray-200 dark:bg-gray-600"></div>
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

        <!-- Content -->
        <div v-else class="space-y-8">
          <!-- Section Députés les plus actifs -->
          <div>
            <h2 class="text-xl font-bold text-gray-900 dark:text-white mb-6">
              Députés les plus actifs
            </h2>
            <div
              class="grid grid-cols-2 gap-4 md:grid-cols-4"
              itemscope
              itemtype="https://schema.org/ItemList"
            >
              <meta itemprop="name" content="Députés les plus actifs" />
              <meta itemprop="numberOfItems" :content="topDeputies.length" />

              <article
                v-for="(deputy, index) in topDeputies"
                :key="deputy.id"
                class="deputy-card group relative"
                :style="{ animationDelay: `${index * 100}ms` }"
                itemscope
                itemtype="https://schema.org/Person"
                itemprop="itemListElement"
              >
                <meta itemprop="position" :content="index + 1" />
                <meta itemprop="identifier" :content="deputy.id" />

                <NuxtLink
                  :to="`/assemblee-nationale/deputes/${deputy.id}/${$getSlugifyUrlPath(deputy.first_name + ' ' + deputy.last_name)}`"
                  class="block h-full"
                  itemprop="url"
                >
                  <div class="deputy-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                    <!-- Badge de position -->
                    <div class="absolute right-3 top-3 z-10">
                      <span
                        class="inline-flex items-center rounded-full px-2 py-1 text-xs font-bold text-white shadow-sm"
                        :class="{
                          'bg-yellow-500': index === 0,
                          'bg-gray-400': index === 1,
                          'bg-amber-600': index === 2,
                          'bg-slate-500': index === 3,
                        }"
                      >
                        {{ index + 1 }}{{ index === 0 ? 'er' : 'ème' }}
                      </span>
                    </div>

                    <div class="p-6">
                      <div class="flex flex-col items-center text-center">
                        <!-- Photo du député -->
                        <div class="relative mb-4">
                          <img
                            :src="getImageUrl(deputy.photo)"
                            :alt="`${deputy.first_name} ${deputy.last_name}`"
                            class="h-20 w-20 rounded-full object-cover ring-2 ring-white shadow-sm transition-transform duration-300 group-hover:scale-105 dark:ring-gray-700"
                            itemprop="image"
                            loading="lazy"
                          />
                          <!-- Indicateur actif -->
                          <div class="absolute -bottom-1 -right-1 h-6 w-6 rounded-full bg-green-400 ring-2 ring-white dark:ring-gray-800 flex items-center justify-center">
                            <UIcon name="i-heroicons-chat-bubble-left-ellipsis" class="h-3 w-3 text-white" />
                          </div>
                        </div>

                        <!-- Info député -->
                        <div class="space-y-1">
                          <h3 class="text-sm font-semibold text-gray-900 dark:text-white capitalize transition-colors duration-200 group-hover:text-blue-600 dark:group-hover:text-blue-400">
                            <span itemprop="givenName">{{ deputy.first_name.toLowerCase() }}</span>
                            <br>
                            <span class="tracking-wider uppercase" itemprop="familyName">{{ deputy.last_name }}</span>
                          </h3>
                          <meta itemprop="name" :content="`${deputy.first_name} ${deputy.last_name}`" />
                          <meta itemprop="jobTitle" content="Député" />

                          <!-- Nombre de questions -->
                          <div class="inline-flex items-center rounded-full bg-blue-100 px-3 py-1 text-xs font-medium text-blue-800 dark:bg-blue-900/30 dark:text-blue-400">
                            {{ deputy.questionsCount }} question{{ deputy.questionsCount > 1 ? 's' : '' }}
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

          <!-- Section Questions -->
          <div
            id="questions-list"
            itemscope
            itemtype="https://schema.org/ItemList"
          >
            <meta itemprop="name" content="Questions écrites parlementaires" />
            <meta itemprop="numberOfItems" :content="questions.length" />

            <!-- Header de section -->
            <div class="mb-6 flex items-center justify-between">
              <h2 class="text-xl font-bold text-gray-900 dark:text-white">Questions écrites</h2>
              <div class="inline-flex items-center rounded-full bg-gray-100 px-3 py-1 text-sm font-medium text-gray-600 dark:bg-gray-800 dark:text-gray-400">
                {{ questions.length }} question{{ questions.length > 1 ? 's' : '' }} au total
              </div>
            </div>

            <!-- Grid des questions -->
            <div class="space-y-4">
              <article
                v-for="(question, index) in paginatedQuestions"
                :key="question.id"
                class="question-card group relative"
                :style="{ animationDelay: `${index * 80}ms` }"
                itemscope
                itemtype="https://schema.org/Question"
                itemprop="itemListElement"
              >
                <meta
                  itemprop="position"
                  :content="(currentPage - 1) * itemsPerPage + index + 1"
                />
                <meta
                  itemprop="url"
                  :content="`${siteUrl}/assemblee-nationale/questions/${question.id}`"
                />
                <meta
                  itemprop="dateCreated"
                  :content="formatDateISO(question.question_date)"
                />

                <div
                  itemprop="author"
                  itemscope
                  itemtype="https://schema.org/Person"
                >
                  <meta
                    itemprop="name"
                    :content="`${question.deputy.first_name} ${question.deputy.last_name}`"
                  />
                  <meta itemprop="jobTitle" content="Député" />
                  <meta
                    itemprop="image"
                    :content="getImageUrl(question.deputy.photo)"
                  />
                </div>

                <NuxtLink
                  :to="`/assemblee-nationale/questions/${question.id}`"
                  class="block h-full"
                >
                  <div class="question-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                    <div class="p-6">
                      <div class="flex gap-4">
                        <!-- Photo du député -->
                        <div class="flex-shrink-0">
                          <div class="relative">
                            <img
                              :src="getImageUrl(question.deputy.photo)"
                              :alt="`${question.deputy.first_name} ${question.deputy.last_name}`"
                              class="h-16 w-16 rounded-full object-cover ring-2 ring-white shadow-sm transition-transform duration-300 group-hover:scale-105 dark:ring-gray-700"
                              itemprop="image"
                              loading="lazy"
                            />
                            <!-- Badge question -->
                            <div class="absolute -bottom-1 -right-1 h-5 w-5 rounded-full bg-blue-500 ring-2 ring-white dark:ring-gray-800 flex items-center justify-center">
                              <UIcon name="i-heroicons-question-mark-circle" class="h-3 w-3 text-white" />
                            </div>
                          </div>
                        </div>

                        <!-- Contenu de la question -->
                        <div class="flex-1 min-w-0">
                          <!-- Date -->
                          <div class="mb-2 flex items-center text-xs text-gray-500 dark:text-gray-400">
                            <UIcon name="i-heroicons-calendar-days" class="mr-1.5 h-3 w-3" />
                            <time
                              :datetime="formatDateISO(question.question_date)"
                              itemprop="dateCreated"
                            >
                              {{ $dateformat(question.question_date) }}
                            </time>
                          </div>

                          <!-- Sujet de la question -->
                          <h3
                            class="text-base font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400 line-clamp-2"
                            itemprop="name"
                          >
                            {{ question.subject }}
                          </h3>

                          <!-- Auteur -->
                          <div class="mt-2 flex items-center text-sm">
                            <span class="font-medium text-blue-700 dark:text-blue-400" itemprop="author">
                              {{ question.deputy.first_name }} {{ question.deputy.last_name }}
                            </span>
                          </div>
                        </div>

                        <!-- Flèche de navigation -->
                        <div class="flex items-center">
                          <div class="flex h-8 w-8 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:scale-110 group-hover:bg-blue-50 dark:bg-gray-700 dark:group-hover:bg-blue-900/30">
                            <UIcon
                              name="i-heroicons-arrow-up-right"
                              class="h-4 w-4 text-gray-400 transition-colors duration-300 group-hover:text-blue-600 dark:group-hover:text-blue-400"
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

            <!-- Pagination moderne -->
            <div v-if="Math.ceil(questions.length / itemsPerPage) > 1" class="mt-8 flex justify-center">
              <nav class="flex items-center space-x-2">
                <button
                  @click="handlePageChange(Math.max(1, currentPage - 1))"
                  :disabled="currentPage <= 1"
                  class="inline-flex h-10 w-10 items-center justify-center rounded-lg bg-white text-gray-500 shadow-sm ring-1 ring-gray-200 hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed dark:bg-gray-800 dark:text-gray-400 dark:ring-gray-700 dark:hover:bg-gray-700"
                >
                  <UIcon name="i-heroicons-chevron-left" class="h-4 w-4" />
                </button>

                <div class="flex items-center space-x-1">
                  <button
                    v-for="page in Math.min(5, Math.ceil(questions.length / itemsPerPage))"
                    :key="page"
                    @click="handlePageChange(page)"
                    :class="page === currentPage
                      ? 'bg-blue-600 text-white shadow-sm'
                      : 'bg-white text-gray-700 hover:bg-gray-50 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700'"
                    class="inline-flex h-10 w-10 items-center justify-center rounded-lg text-sm font-medium ring-1 ring-gray-200 dark:ring-gray-700"
                  >
                    {{ page }}
                  </button>
                </div>

                <button
                  @click="handlePageChange(Math.min(Math.ceil(questions.length / itemsPerPage), currentPage + 1))"
                  :disabled="currentPage >= Math.ceil(questions.length / itemsPerPage)"
                  class="inline-flex h-10 w-10 items-center justify-center rounded-lg bg-white text-gray-500 shadow-sm ring-1 ring-gray-200 hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed dark:bg-gray-800 dark:text-gray-400 dark:ring-gray-700 dark:hover:bg-gray-700"
                >
                  <UIcon name="i-heroicons-chevron-right" class="h-4 w-4" />
                </button>
              </nav>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
