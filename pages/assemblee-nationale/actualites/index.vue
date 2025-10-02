<script setup lang="ts">
import { useNews } from "~/composables/news/useNews";

const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();

const title =
  "Actualités de l'Assemblée nationale du Sénégal | Vie-Publique.sn";
const description =
  "Suivez toutes les actualités de l'Assemblée nationale du Sénégal. Débats parlementaires, votes, commissions et activités des députés en temps réel.";
const url = `${siteUrl}/assemblee-nationale/actualites`;
const image = `${siteUrl}/images/assemblee-nationale-actualites.webp`;

const newsCollectionSchema = {
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
    url: `${siteUrl}/assemblee-nationale`,
  },
  mainEntity: {
    "@type": "ItemList",
    name: "Actualités Assemblée nationale",
    description:
      "Liste des dernières actualités de l'Assemblée nationale du Sénégal",
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
      name: "Actualités",
      item: url,
    },
  ],
};

const organizationSchema = {
  "@context": "https://schema.org",
  "@type": "NewsMediaOrganization",
  name: siteName,
  url: siteUrl,
  logo: defaultImage,
  sameAs: ["https://twitter.com/viepubliquesn"],
  address: {
    "@type": "PostalAddress",
    addressCountry: "SN",
    addressLocality: "Dakar",
  },
  publishingPrinciples: `${siteUrl}/ethique`,
  correctionsPolicy: `${siteUrl}/corrections`,
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
    "actualités Assemblée nationale Sénégal",
    "débats parlementaires Sénégal",
    "votes députés Sénégal",
    "commissions parlementaires",
    "activité législative Sénégal",
    "parlement sénégalais news",
    "politique sénégalaise actualités",
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
    {
      name: "news_keywords",
      content:
        "Assemblée nationale, Sénégal, politique, débats, votes, députés",
    },
  ],
  script: [
    {
      type: "application/ld+json",
      children: JSON.stringify(newsCollectionSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(breadcrumbSchema),
    },
    {
      type: "application/ld+json",
      children: JSON.stringify(organizationSchema),
    },
  ],
});

const { news, loading, error } = useNews({ category: "assemblee-nationale" });

const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString("fr-FR", {
    year: "numeric",
    month: "long",
    day: "numeric",
  });
};

const formatDateISO = (date: string) => {
  return new Date(date).toISOString();
};
</script>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes assemblyNewsCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.assembly-news-card {
  animation: assemblyNewsCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.assembly-news-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .assembly-news-card-inner {
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
.assembly-news-skeleton {
  animation: assemblyNewsCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Line clamp pour les titres */
.line-clamp-2 {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  line-height: 1.5;
  max-height: 3em;
}

/* États focus pour l'accessibilité */
.assembly-news-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .assembly-news-card-inner {
    border-radius: 1rem;
  }

  .assembly-news-card-inner .p-6 {
    padding: 1.25rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .assembly-news-card {
    animation: none;
    opacity: 1;
  }

  .skeleton-shimmer {
    animation: none;
  }

  .group:hover .assembly-news-card-inner {
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
.assembly-news-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .assembly-news-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .assembly-news-card {
    break-inside: avoid;
  }

  .assembly-news-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .assembly-news-card-inner {
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

/* Glow effect pour le badge assemblée */
.group:hover [class*="bg-blue-600"] {
  box-shadow: 0 0 20px rgba(37, 99, 235, 0.4);
}

/* Hover effects pour les images */
.assembly-news-card img {
  transition: transform 0.3s ease-out, filter 0.3s ease-out;
}

.group:hover .assembly-news-card img {
  filter: brightness(1.05) contrast(1.05);
}

/* Effet spécial pour le badge avec pulse */
@keyframes badgePulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
}

.group:hover [class*="bg-blue-600"] {
  animation: badgePulse 2s ease-in-out infinite;
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
          Actualités de l'Assemblée
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4">
          Suivez l'activité parlementaire en temps réel : débats, votes, commissions et initiatives législatives
        </p>
      </div>

      <!-- Content section -->
      <div class="assembly-news-container">
        <!-- Loading state moderne -->
        <div
          v-if="loading"
          class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3"
        >
          <div
            v-for="n in 6"
            :key="n"
            class="assembly-news-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 100}ms` }"
          >
            <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <!-- Image skeleton -->
              <div class="aspect-[16/9] bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                <div class="skeleton-shimmer h-full w-full"></div>
              </div>

              <!-- Content skeleton -->
              <div class="p-6">
                <div class="space-y-3">
                  <div class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-4 w-24 rounded bg-gray-200 dark:bg-gray-600"></div>
                </div>
                <div class="mt-4 flex gap-2">
                  <div class="h-6 w-16 rounded-full bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-6 w-20 rounded-full bg-gray-200 dark:bg-gray-600"></div>
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
          v-else-if="!news.length"
          class="text-center py-12"
        >
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-newspaper" class="h-8 w-8 text-gray-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Aucune actualité disponible
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Les actualités de l'Assemblée seront affichées ici
          </p>
        </div>

        <!-- Grid des actualités -->
        <div
          v-else
          class="grid gap-6 sm:grid-cols-2 lg:grid-cols-3"
          itemscope
          itemtype="https://schema.org/ItemList"
        >
          <meta itemprop="numberOfItems" :content="news.length" />

          <article
            v-for="(article, index) in news"
            :key="article.id"
            class="assembly-news-card group relative"
            :style="{ animationDelay: `${index * 100}ms` }"
            itemscope
            itemtype="https://schema.org/NewsArticle"
            itemprop="itemListElement"
          >
            <meta itemprop="position" :content="index + 1" />
            <meta
              itemprop="url"
              :content="`${siteUrl}/assemblee-nationale/actualites/${article.id}/${article.slug}`"
            />
            <meta
              itemprop="datePublished"
              :content="formatDateISO(article.date_published)"
            />
            <meta itemprop="publisher" content="Vie-Publique.sn" />

            <div
              itemprop="author"
              itemscope
              itemtype="https://schema.org/Organization"
            >
              <meta itemprop="name" content="Assemblée nationale du Sénégal" />
              <meta itemprop="url" :content="`${siteUrl}/assemblee-nationale`" />
            </div>

            <NuxtLink
              :to="`/assemblee-nationale/actualites/${article.id}/${article.slug}`"
              class="block h-full"
              itemprop="url"
            >
              <div class="assembly-news-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                <!-- Image avec overlay -->
                <div class="relative aspect-[16/9] overflow-hidden">
                  <div
                    itemprop="image"
                    itemscope
                    itemtype="https://schema.org/ImageObject"
                  >
                    <img
                      :src="$directusImageUrl(article.cover_image, '50')"
                      :alt="article.title"
                      class="h-full w-full object-cover transition-transform duration-300 group-hover:scale-105"
                      itemprop="contentUrl"
                      loading="lazy"
                    />
                    <meta
                      itemprop="url"
                      :content="$directusImageUrl(article.cover_image, '50')"
                    />
                    <meta itemprop="width" content="400" />
                    <meta itemprop="height" content="225" />
                  </div>

                  <!-- Overlay gradient -->
                  <div class="absolute inset-0 bg-gradient-to-t from-black/20 via-transparent to-transparent opacity-0 transition-opacity duration-300 group-hover:opacity-100"></div>

                  <!-- Badge assemblée -->
                  <div class="absolute left-4 top-4">
                    <span class="inline-flex items-center rounded-full bg-blue-600 px-3 py-1 text-xs font-medium text-white shadow-lg backdrop-blur-sm">
                      <UIcon name="i-heroicons-building-library" class="mr-1.5 h-3 w-3" />
                      Assemblée
                    </span>
                  </div>
                </div>

                <!-- Contenu -->
                <div class="p-6">
                  <h2
                    class="mb-3 text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400 line-clamp-2"
                    itemprop="headline"
                  >
                    {{ article.title }}
                  </h2>

                  <!-- Date -->
                  <div class="mb-3 flex items-center text-sm text-gray-500 dark:text-gray-400">
                    <UIcon name="i-heroicons-calendar-days" class="mr-1.5 h-4 w-4" />
                    <time
                      :datetime="formatDateISO(article.date_published)"
                      itemprop="datePublished"
                    >
                      {{ formatDate(article.date_published) }}
                    </time>
                  </div>

                  <!-- Tags -->
                  <div class="flex flex-wrap gap-2">
                    <template v-if="article.tags?.length">
                      <span
                        v-for="tag in article.tags.slice(0, 2)"
                        :key="tag"
                        class="inline-flex items-center rounded-full bg-gray-100 px-2.5 py-1 text-xs font-medium text-gray-600 dark:bg-gray-700 dark:text-gray-300"
                        itemprop="keywords"
                      >
                        {{ tag }}
                      </span>
                      <span
                        v-if="article.tags.length > 2"
                        class="inline-flex items-center rounded-full bg-gray-100 px-2.5 py-1 text-xs font-medium text-gray-600 dark:bg-gray-700 dark:text-gray-300"
                      >
                        +{{ article.tags.length - 2 }}
                      </span>
                    </template>
                    <template v-else>
                      <span
                        class="inline-flex items-center rounded-full bg-blue-100 px-2.5 py-1 text-xs font-medium text-blue-800 dark:bg-blue-900/30 dark:text-blue-400"
                        itemprop="keywords"
                      >
                        Assemblée nationale
                      </span>
                    </template>
                  </div>
                </div>

                <!-- Effet de border animé -->
                <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20"></div>
              </div>

              <!-- Schema.org mainEntityOfPage -->
              <div
                itemprop="mainEntityOfPage"
                itemscope
                itemtype="https://schema.org/WebPage"
              >
                <meta
                  itemprop="@id"
                  :content="`${siteUrl}/assemblee-nationale/actualites/${article.id}/${article.slug}`"
                />
              </div>
            </NuxtLink>
          </article>
        </div>
      </div>
    </div>
  </div>
</template>
