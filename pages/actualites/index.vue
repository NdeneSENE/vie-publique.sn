<script setup lang="ts">
import { useNewsStore } from "~/stores/news";

const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const title = "Actualités de la République du Sénégal | Vie-Publique.sn";
const description = "Suivez toute l'actualité de la République du Sénégal. Conseil des ministres, Assemblée nationale, vie politique et institutionnelle sénégalaise.";
const url = `${siteUrl}/actualites`;
const image = `${siteUrl}/images/share-linkedin.png`;

// Schemas SEO optimisés (gardés identiques pour le référencement)
const newsCollectionSchema = {
  "@context": "https://schema.org",
  "@type": "CollectionPage",
  "name": title,
  "description": description,
  "url": url,
  "image": image,
  "isPartOf": {
    "@type": "WebSite",
    "name": siteName,
    "url": siteUrl,
  },
  "about": {
    "@type": "GovernmentOrganization",
    "name": "République du Sénégal",
    "description": "État souverain d'Afrique de l'Ouest",
  },
  "mainEntity": {
    "@type": "ItemList",
    "name": "Actualités République du Sénégal",
    "description": "Collection des dernières actualités de la République du Sénégal",
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
      "name": "Actualités",
      "item": url,
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
    "actualités Sénégal",
    "news République du Sénégal",
    "Conseil des ministres actualités",
    "Assemblée nationale news",
    "politique sénégalaise actualités",
    "gouvernement Sénégal news",
    "information République Sénégal",
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
    { name: "news_keywords", content: "Sénégal, actualités, politique, gouvernement, République" },
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
  ],
});

// Store et logique
const store = useNewsStore();
const searchQuery = computed({
  get: () => store.searchQuery,
  set: (value) => store.setSearchQuery(value),
});

const selectedCategory = computed({
  get: () => store.selectedCategory,
  set: (value) => store.setSelectedCategory(value),
});

// Chargement des données
onBeforeMount(async () => {
  await store.fetchNews();
});

const route = useRoute();
watch(
  () => route.path,
  async () => {
    if (route.path === "/actualites") {
      await store.fetchNews();
    }
  },
);

// Fonctions utilitaires
const formatNewsUrl = (article: {
  id: string;
  title?: string;
  slug?: string;
  category?: {
    slug?: string;
  };
}) => {
  if (!article) return "/actualites";

  const id = article.id;
  const slug =
    article.slug ||
    (article.title
      ? article.title
          .toLowerCase()
          .replace(/[^a-z0-9]+/g, "-")
          .replace(/(^-|-$)/g, "")
      : "actualite");

  const categorySlug = article.category?.slug;

  if (categorySlug === "conseil-des-ministres") {
    return `/conseil-des-ministres/${id}/${slug}`;
  }

  if (categorySlug === "assemblee-nationale") {
    return `/assemblee-nationale/actualites/${id}/${slug}`;
  }

  return `/actualites/${id}/${slug}`;
};

const getCategoryConfig = (categoryName: string) => {
  const configs = {
    "Conseil des ministres": { 
      color: "bg-amber-500", 
      textColor: "text-amber-700 dark:text-amber-400",
      bgColor: "bg-amber-50 dark:bg-amber-900/20"
    },
    "Assemblée nationale": { 
      color: "bg-blue-500", 
      textColor: "text-blue-700 dark:text-blue-400",
      bgColor: "bg-blue-50 dark:bg-blue-900/20"
    },
    "Article": { 
      color: "bg-emerald-500", 
      textColor: "text-emerald-700 dark:text-emerald-400",
      bgColor: "bg-emerald-50 dark:bg-emerald-900/20"
    },
    "Toutes": { 
      color: "bg-gray-500", 
      textColor: "text-gray-700 dark:text-gray-400",
      bgColor: "bg-gray-50 dark:bg-gray-900/20"
    }
  };
  
  return configs[categoryName as keyof typeof configs] || configs["Toutes"];
};

const formatDateISO = (date: string) => {
  return new Date(date).toISOString();
};
</script>

<template>
  <div class="py-6 sm:py-8">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8" itemscope itemtype="https://schema.org/CollectionPage">
      <!-- Header épuré -->
      <div class="text-center mb-8">
        <h1 class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white" itemprop="headline">
          Actualités
        </h1>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300">
          L'actualité officielle de la République du Sénégal
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
              placeholder="Rechercher dans les actualités..."
              class="w-full rounded-xl border-0 bg-white py-3 pl-12 pr-4 text-gray-900 shadow-sm ring-1 ring-gray-200 placeholder:text-gray-400 focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 sm:text-sm dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:placeholder:text-gray-500 dark:focus:ring-blue-400"
              :disabled="store.loading"
            />
          </div>
        </div>
      </div>

      <!-- Filtres par catégorie épurés -->
      <div class="mb-8">
        <!-- Skeleton pour les filtres -->
        <div v-if="store.loading" class="flex justify-center">
          <div class="flex flex-wrap justify-center gap-2">
            <div
              v-for="n in 4"
              :key="n"
              class="h-8 w-24 animate-pulse rounded-full bg-gray-200 dark:bg-gray-700"
            />
          </div>
        </div>

        <!-- Filtres de catégories -->
        <div v-else class="flex justify-center">
          <div class="flex flex-wrap justify-center gap-2">
            <button
              v-for="category in store.categories"
              :key="category.name"
              @click="selectedCategory = category.name"
              class="inline-flex items-center rounded-full px-4 py-2 text-sm font-medium transition-all duration-200"
              :class="selectedCategory === category.name
                ? [getCategoryConfig(category.name).bgColor, getCategoryConfig(category.name).textColor, 'ring-2 ring-current ring-opacity-20']
                : 'bg-gray-100 text-gray-700 hover:bg-gray-200 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700'"
            >
              <div
                class="mr-2 h-2 w-2 rounded-full"
                :class="getCategoryConfig(category.name).color"
              />
              {{ category.name }}
            </button>
          </div>
        </div>
      </div>

      <!-- Contenu principal -->
      <div>
        <!-- Loading state -->
        <div
          v-if="store.loading"
          class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3"
        >
          <div 
            v-for="n in 9" 
            :key="n" 
            class="news-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 100}ms` }"
          >
            <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <!-- Image skeleton -->
              <div class="aspect-[16/9] bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                <div class="skeleton-shimmer h-full w-full"></div>
              </div>
              
              <!-- Content skeleton -->
              <div class="p-4">
                <div class="mb-3 h-5 w-20 rounded-full bg-gray-200 dark:bg-gray-600"></div>
                <div class="space-y-2">
                  <div class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                </div>
                <div class="mt-3 h-4 w-24 rounded bg-gray-200 dark:bg-gray-600"></div>
              </div>
            </div>
          </div>
        </div>

        <!-- Error state -->
        <div v-else-if="store.error" class="text-center py-12">
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-red-100 dark:bg-red-900/30">
            <UIcon name="i-heroicons-exclamation-triangle" class="h-8 w-8 text-red-600 dark:text-red-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Erreur de chargement
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            {{ store.error }}
          </p>
          <button
            @click="store.fetchNews()"
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
          >
            Réessayer
          </button>
        </div>

        <!-- Empty state -->
        <div
          v-else-if="!store.articles.length || store.paginatedNews.length === 0"
          class="text-center py-12"
        >
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-newspaper" class="h-8 w-8 text-gray-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Aucune actualité trouvée
          </h3>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Essayez de modifier vos filtres ou votre recherche
          </p>
        </div>

        <!-- Grille des actualités -->
        <div v-else>
          <div 
            class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3"
            itemscope 
            itemtype="https://schema.org/ItemList"
            itemprop="mainEntity"
          >
            <meta itemprop="numberOfItems" :content="store.paginatedNews.length">
            
            <article
              v-for="(article, index) in store.paginatedNews"
              :key="article.id"
              itemscope
              itemtype="https://schema.org/NewsArticle"
              itemprop="itemListElement"
              class="news-card group relative"
              :style="{ animationDelay: `${index * 100}ms` }"
            >
              <meta itemprop="position" :content="index + 1">
              <meta itemprop="url" :content="`${siteUrl}${formatNewsUrl(article)}`">
              <meta itemprop="datePublished" :content="formatDateISO(article.date_published)">
              
              <div itemprop="author" itemscope itemtype="https://schema.org/Organization">
                <meta itemprop="name" :content="siteName">
              </div>

              <div itemprop="publisher" itemscope itemtype="https://schema.org/Organization">
                <meta itemprop="name" :content="siteName">
                <meta itemprop="url" :content="siteUrl">
              </div>

              <NuxtLink :to="formatNewsUrl(article)" class="block h-full" itemprop="url">
                <div class="news-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-lg group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                  <!-- Image avec overlay -->
                  <div class="relative aspect-[16/9] overflow-hidden">
                    <div itemprop="image" itemscope itemtype="https://schema.org/ImageObject">
                      <NuxtImg
                        :src="
                          article.cover_image
                            ? $directusImageUrl(article.cover_image, '50')
                            : '/default-image-2.gif'
                        "
                        :alt="article.title || 'Image actualité'"
                        class="h-full w-full object-cover transition-transform duration-300 group-hover:scale-105"
                        loading="lazy"
                        fetchpriority="high"
                        sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
                        :placeholder="[400, 225]"
                        itemprop="contentUrl"
                      />
                      <meta itemprop="url" :content="article.cover_image ? $directusImageUrl(article.cover_image, '50') : '/default-image-2.gif'">
                      <meta itemprop="width" content="400">
                      <meta itemprop="height" content="225">
                    </div>
                    
                    <!-- Overlay gradient -->
                    <div class="absolute inset-0 bg-gradient-to-t from-black/20 via-transparent to-transparent opacity-0 transition-opacity duration-300 group-hover:opacity-100"></div>
                    
                    <!-- Badge catégorie -->
                    <div class="absolute left-4 top-4">
                      <span
                        class="inline-flex items-center rounded-full px-3 py-1 text-xs font-medium text-white shadow-lg backdrop-blur-sm bg-black/60"
                        itemprop="articleSection"
                      >
                        <div
                          class="mr-2 h-2 w-2 rounded-full"
                          :class="getCategoryConfig(article.category?.name || 'Non catégorisé').color"
                        />
                        {{ article.category?.name || "Actualité" }}
                      </span>
                    </div>
                  </div>

                  <!-- Contenu -->
                  <div class="p-4">
                    <h2
                      class="line-clamp-2 text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400"
                      itemprop="headline"
                    >
                      {{ article.title }}
                    </h2>

                    <div class="mt-3 flex items-center text-sm text-gray-500 dark:text-gray-400">
                      <UIcon name="i-heroicons-calendar-days" class="mr-1.5 h-4 w-4" />
                      <time 
                        :datetime="formatDateISO(article.date_published)"
                        itemprop="datePublished"
                      >
                        {{ $dateformatWithDayName(article.date_published) }}
                      </time>
                    </div>
                  </div>

                  <!-- Effet de border animé -->
                  <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20"></div>
                </div>

                <!-- Main entity of page -->
                <div itemprop="mainEntityOfPage" itemscope itemtype="https://schema.org/WebPage">
                  <meta itemprop="@id" :content="`${siteUrl}${formatNewsUrl(article)}`">
                </div>
              </NuxtLink>
            </article>
          </div>

          <!-- Pagination moderne -->
          <div v-if="store.totalPages > 1" class="mt-12 flex justify-center">
            <nav class="flex items-center space-x-2">
              <button
                @click="store.currentPage = Math.max(1, store.currentPage - 1)"
                :disabled="store.currentPage <= 1"
                class="inline-flex h-10 w-10 items-center justify-center rounded-lg bg-white text-gray-500 shadow-sm ring-1 ring-gray-200 hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed dark:bg-gray-800 dark:text-gray-400 dark:ring-gray-700 dark:hover:bg-gray-700"
              >
                <UIcon name="i-heroicons-chevron-left" class="h-4 w-4" />
              </button>

              <div class="flex items-center space-x-1">
                <button
                  v-for="page in Math.min(5, store.totalPages)"
                  :key="page"
                  @click="store.currentPage = page"
                  :class="page === store.currentPage
                    ? 'bg-blue-600 text-white shadow-sm'
                    : 'bg-white text-gray-700 hover:bg-gray-50 dark:bg-gray-800 dark:text-gray-300 dark:hover:bg-gray-700'"
                  class="inline-flex h-10 w-10 items-center justify-center rounded-lg text-sm font-medium ring-1 ring-gray-200 dark:ring-gray-700"
                >
                  {{ page }}
                </button>
              </div>

              <button
                @click="store.currentPage = Math.min(store.totalPages, store.currentPage + 1)"
                :disabled="store.currentPage >= store.totalPages"
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
</template>

<style scoped>
/* Animation d'apparition des cartes */
@keyframes newsCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.news-card {
  animation: newsCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* Effet de lift pour les cartes */
.news-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}

.group:hover .news-card-inner {
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
.news-skeleton {
  animation: newsCardFadeIn 0.6s ease-out forwards;
  opacity: 0;
}

/* États focus pour l'accessibilité */
.news-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .news-card-inner {
    border-radius: 1rem;
  }
  
  .news-card-inner .p-4 {
    padding: 1rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .news-card {
    animation: none;
    opacity: 1;
  }
  
  .skeleton-shimmer {
    animation: none;
  }
  
  .group:hover .news-card-inner {
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
.news-card-inner {
  will-change: transform, box-shadow;
}

.group:hover .news-card-inner {
  will-change: auto;
}

/* Print styles */
@media print {
  .news-card {
    break-inside: avoid;
  }
  
  .news-card-inner {
    box-shadow: none !important;
    transform: none !important;
  }
}

/* Dark mode enhancements */
.dark .news-card-inner {
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
</style>