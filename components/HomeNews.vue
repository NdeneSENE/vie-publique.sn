<script lang="ts" setup>
import { useNewsStore } from "~/stores/news";

// Utilisation du store
const store = useNewsStore();

// Fonction pour formater l'URL selon le nouveau format /categorie/id/slug
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

  // Gestion spécifique selon la catégorie
  const categorySlug = article.category?.slug;

  // Cas du conseil des ministres
  if (categorySlug === "conseil-des-ministres") {
    return `/conseil-des-ministres/${id}/${slug}`;
  }

  // Cas de l'assemblée nationale
  if (categorySlug === "assemblee-nationale") {
    return `/assemblee-nationale/actualites/${id}/${slug}`;
  }

  // Cas par défaut pour toutes les autres catégories
  return `/actualites/${id}/${slug}`;
};

// Fonction pour obtenir la couleur de catégorie
const getCategoryColor = (categorySlug?: string) => {
  const colors = {
    'conseil-des-ministres': 'bg-amber-100 text-amber-800 dark:bg-amber-900/30 dark:text-amber-400',
    'assemblee-nationale': 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-400',
    'budget': 'bg-emerald-100 text-emerald-800 dark:bg-emerald-900/30 dark:text-emerald-400',
    'elections': 'bg-purple-100 text-purple-800 dark:bg-purple-900/30 dark:text-purple-400',
    default: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-400'
  };
  
  return colors[categorySlug as keyof typeof colors] || colors.default;
};

// Fonction pour obtenir le nom de la catégorie
const getCategoryName = (categorySlug?: string) => {
  const names = {
    'conseil-des-ministres': 'Conseil des ministres',
    'assemblee-nationale': 'Assemblée Nationale',
    'budget': 'Budget',
    'elections': 'Élections'
  };
  
  return names[categorySlug as keyof typeof names] || 'Actualités';
};

// Récupération des articles au montage du composant
onMounted(async () => {
  await store.fetchNews({ featured: true });
});
</script>

<template>
  <div class="py-8 sm:py-12">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <!-- Header de section moderne -->
      <div class="text-center">
        <h2 class="text-2xl font-bold tracking-tight text-gray-900 sm:text-3xl dark:text-white">
          À la une
        </h2>
        <p class="mt-3 text-lg text-gray-600 dark:text-gray-300 sm:mt-4">
          Les dernières actualités de la République du Sénégal
        </p>
      </div>

      <!-- States de chargement et erreur -->
      <div class="mt-8 sm:mt-12">
        <!-- Loading state -->
        <div
          v-if="store.loading"
          class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3"
        >
          <div 
            v-for="n in 3" 
            :key="n" 
            class="news-skeleton group animate-pulse"
            :style="{ animationDelay: `${n * 150}ms` }"
          >
            <div class="overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <!-- Image skeleton -->
              <div class="aspect-[16/9] bg-gradient-to-r from-gray-200 via-gray-100 to-gray-200 dark:from-gray-700 dark:via-gray-600 dark:to-gray-700">
                <div class="skeleton-shimmer h-full w-full"></div>
              </div>
              
              <!-- Content skeleton -->
              <div class="p-6">
                <div class="mb-3 h-4 w-20 rounded-full bg-gray-200 dark:bg-gray-600"></div>
                <div class="space-y-2">
                  <div class="h-5 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-5 w-3/4 rounded bg-gray-200 dark:bg-gray-600"></div>
                </div>
                <div class="mt-4 h-4 w-24 rounded bg-gray-200 dark:bg-gray-600"></div>
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
            Une erreur est survenue lors du chargement des actualités.
          </p>
          <button
            @click="store.fetchNews({ featured: true })"
            class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
          >
            Réessayer
          </button>
        </div>

        <!-- Content -->
        <div v-else>
          <!-- Grid des articles -->
          <div class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3">
            <article
              v-for="(article, index) in store.featuredNews?.slice(0, 3)"
              :key="article.id"
              class="news-card group relative"
              :style="{ animationDelay: `${index * 150}ms` }"
            >
              <NuxtLink
                :to="formatNewsUrl(article)"
                class="block h-full"
              >
                <div class="news-card-inner overflow-hidden rounded-2xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-xl group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                  <!-- Image avec overlay gradient -->
                  <div class="relative aspect-[16/9] overflow-hidden">
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
                      :placeholder="[300, 169]"
                    />
                    
                    <!-- Overlay gradient -->
                    <div class="absolute inset-0 bg-gradient-to-t from-black/20 via-transparent to-transparent opacity-0 transition-opacity duration-300 group-hover:opacity-100"></div>
                    
                    <!-- Badge catégorie -->
                    <div 
                      v-if="article.category?.slug"
                      class="absolute left-4 top-4"
                    >
                      <span 
                        class="inline-flex items-center rounded-full px-3 py-1 text-xs font-medium backdrop-blur-sm"
                        :class="getCategoryColor(article.category.slug)"
                      >
                        {{ getCategoryName(article.category.slug) }}
                      </span>
                    </div>
                  </div>

                  <!-- Contenu -->
                  <div class="p-6">
                    <!-- Titre -->
                    <h3 class="line-clamp-2 text-lg font-semibold leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400">
                      {{ article.title }}
                    </h3>

                    <!-- Metadata -->
                    <div class="mt-4 flex items-center justify-between">
                      <div
                        v-if="article.date_published"
                        class="flex items-center text-sm text-gray-500 dark:text-gray-400"
                      >
                        <UIcon name="i-heroicons-calendar-days" class="mr-1.5 h-4 w-4" />
                        {{ $dateformatWithDayName(article.date_published) }}
                      </div>
                      
                      <!-- Flèche de navigation -->
                      <div class="flex h-8 w-8 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:bg-blue-50 group-hover:scale-110 dark:bg-gray-700 dark:group-hover:bg-blue-900/30">
                        <UIcon 
                          name="i-heroicons-arrow-up-right" 
                          class="h-4 w-4 text-gray-400 transition-colors duration-300 group-hover:text-blue-600 dark:group-hover:text-blue-400" 
                        />
                      </div>
                    </div>
                  </div>

                  <!-- Effet de border animé -->
                  <div class="absolute inset-0 rounded-2xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20"></div>
                </div>
              </NuxtLink>
            </article>
          </div>

          <!-- Call to action -->
          <div class="mt-12 text-center">
            <NuxtLink
              to="/actualites"
              class="group inline-flex items-center rounded-full bg-white px-6 py-3 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 transition-all duration-200 hover:bg-gray-50 hover:shadow-md hover:ring-gray-400 dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:hover:bg-gray-700 dark:hover:ring-gray-600"
            >
              Voir toutes les actualités
              <UIcon 
                name="i-heroicons-arrow-right" 
                class="ml-2 h-4 w-4 transition-transform duration-200 group-hover:translate-x-1" 
              />
            </NuxtLink>
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

/* Responsive améliorations */
@media (max-width: 640px) {
  .news-card-inner {
    border-radius: 1rem;
  }
  
  .news-card-inner .p-6 {
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
}

/* Focus states pour l'accessibilité */
.news-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 1rem;
}

/* Amélioration du contraste */
@media (prefers-contrast: high) {
  .news-card-inner {
    border: 2px solid;
  }
  
  .dark .text-gray-400 {
    color: rgb(156, 163, 175);
  }
}

/* Effet de glow pour certains badges */
.group:hover [class*="bg-amber-"] {
  box-shadow: 0 0 20px rgba(245, 158, 11, 0.3);
}

.group:hover [class*="bg-blue-"] {
  box-shadow: 0 0 20px rgba(59, 130, 246, 0.3);
}

.group:hover [class*="bg-emerald-"] {
  box-shadow: 0 0 20px rgba(16, 185, 129, 0.3);
}

.group:hover [class*="bg-purple-"] {
  box-shadow: 0 0 20px rgba(147, 51, 234, 0.3);
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

/* Hover effects pour les images */
.news-card img {
  transition: transform 0.3s ease-out, filter 0.3s ease-out;
}

.group:hover .news-card img {
  filter: brightness(1.05) contrast(1.05);
}

/* Line clamp fallback pour les anciens navigateurs */
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