<script setup lang="ts">
import { useNews } from "~/composables/news/useNews";

const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();
const config = useRuntimeConfig();
const route = useRoute();

const { article, loading, error, fetchNewsById } = useNews({
  category: "conseil-des-ministres",
});

onMounted(async () => {
  if (route.params.id) {
    await fetchNewsById(route.params.id as string);
  }
});

const title = computed(() => {
  if (!article.value) return "Communiqué Conseil des ministres Sénégal";
  return `${article.value.title} | Conseil des ministres du Sénégal`;
});

const description = computed(() => {
  if (!article.value)
    return "Communiqué conseil des ministres du gouvernement du Sénégal";

  const htmlContent = article.value.content || article.value.title;
  const textContent = htmlContent
    .replace(/<[^>]*>/g, " ")
    .replace(/\s+/g, " ")
    .trim();
  const truncatedContent =
    textContent.length > 160
      ? `${textContent.substring(0, 157)}...`
      : textContent;

  return truncatedContent || article.value.title;
});

const url = computed(
  () =>
    `${siteUrl}/conseil-des-ministres/${route.params.id}/${route.params.slug}`,
);

const image = computed(() => {
  if (article.value?.cover_image) {
    return article.value.cover_image.startsWith("http")
      ? article.value.cover_image
      : `${siteUrl}${article.value.cover_image}`;
  }
  return `${siteUrl}/images/share-conseil-des-ministres-nomination-full.jfif`;
});

const publishedDate = computed(() =>
  article.value?.date_published
    ? new Date(article.value.date_published).toISOString()
    : null,
);

const modifiedDate = computed(() =>
  article.value?.date_updated
    ? new Date(article.value.date_updated).toISOString()
    : publishedDate.value,
);

// Schemas SEO (gardés identiques)
const articleSchema = computed(() => ({
  "@context": "https://schema.org",
  "@type": "GovernmentAnnouncement",
  headline: article.value?.title || "Communiqué du Conseil des ministres",
  name: article.value?.title || "Communiqué du Conseil des ministres",
  description: description.value,
  url: url.value,
  image: {
    "@type": "ImageObject",
    url: image.value,
    width: 1200,
    height: 630,
  },
  datePublished: publishedDate.value,
  dateModified: modifiedDate.value || publishedDate.value,
  author: {
    "@type": "GovernmentOrganization",
    name: "Conseil des ministres du Sénégal",
    url: `${siteUrl}/conseil-des-ministres`,
  },
  publisher: {
    "@type": "GovernmentOrganization",
    name: "Conseil des ministres du Sénégal",
    url: `${siteUrl}/conseil-des-ministres`,
    logo: {
      "@type": "ImageObject",
      url: `${siteUrl}/images/logo-senegal.png`,
      width: 200,
      height: 200,
    },
  },
  mainEntityOfPage: {
    "@type": "WebPage",
    "@id": url.value,
  },
  articleSection: "Gouvernement",
  genre: "Communiqué officiel",
  keywords: [
    "Conseil des ministres",
    "Sénégal",
    "Gouvernement",
    "Communiqué officiel",
    "Bassirou Diomaye Faye",
    "Ousmane Sonko",
  ],
  about: {
    "@type": "GovernmentOrganization",
    name: "Conseil des ministres du Sénégal",
    parentOrganization: {
      "@type": "GovernmentOrganization",
      name: "République du Sénégal",
    },
  },
  isPartOf: {
    "@type": "WebSite",
    name: siteName,
    url: siteUrl,
  },
}));

const breadcrumbSchema = computed(() => ({
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
      name: "Conseil des ministres",
      item: `${siteUrl}/conseil-des-ministres`,
    },
    {
      "@type": "ListItem",
      position: 3,
      name: article.value?.title || "Communiqué",
      item: url.value,
    },
  ],
}));

// Configuration SEO complète
useSeoMeta({
  title: title.value,
  ogTitle: title.value,
  description: description.value,
  ogDescription: description.value,
  ogImage: image.value,
  ogUrl: url.value,
  twitterCard: "summary_large_image",
  twitterTitle: title.value,
  twitterDescription: description.value,
  twitterImage: image.value,
  keywords: [
    ...keywords,
    "Conseil des ministres Sénégal",
    "communiqué conseil des ministres",
    "gouvernement Sénégal",
    "décisions gouvernementales",
    "Bassirou Diomaye Faye",
    "Ousmane Sonko",
    "politique sénégalaise",
    "République du Sénégal",
  ].join(", "),
});

useHead({
  htmlAttrs: { lang: "fr-SN" },
  link: [
    { rel: "canonical", href: url.value },
    { rel: "alternate", hreflang: "fr-SN", href: url.value },
    { rel: "alternate", hreflang: "fr", href: url.value },
  ],
  meta: [
    { name: "theme-color", content: themeColor },
    { name: "author", content: "Conseil des ministres du Sénégal" },
    { property: "og:type", content: "article" },
    { property: "og:site_name", content: siteName },
    { property: "article:published_time", content: publishedDate.value },
    { property: "article:modified_time", content: modifiedDate.value },
    { property: "article:author", content: "Conseil des ministres du Sénégal" },
    { property: "article:section", content: "Gouvernement" },
    {
      property: "article:tag",
      content: "Conseil des ministres, Sénégal, Gouvernement",
    },
    { name: "robots", content: "index, follow, max-image-preview:large" },
    {
      name: "googlebot",
      content:
        "index, follow, max-snippet:-1, max-image-preview:large, max-video-preview:-1",
    },
    { name: "geo.region", content: "SN" },
    { name: "geo.placename", content: "Dakar" },
    { name: "geo.position", content: "14.7645042;-17.3660286" },
    { name: "ICBM", content: "14.7645042, -17.3660286" },
    {
      name: "news_keywords",
      content: "Conseil des ministres, Sénégal, gouvernement, communiqué",
    },
    { name: "category", content: "Government" },
    { name: "coverage", content: "Worldwide" },
    { name: "distribution", content: "Global" },
    { name: "rating", content: "General" },
  ],
  script: [
    {
      type: "application/ld+json",
      children: () => JSON.stringify(articleSchema.value),
    },
    {
      type: "application/ld+json",
      children: () => JSON.stringify(breadcrumbSchema.value),
    },
  ],
});

// Fonctions utilitaires
const getAssetUrl = (assetId: string, slug: string) => {
  return `${config.public.cmsApiUrl}/assets/${assetId}/${slug}.pdf`;
};

const formatDateISO = (date: string) => {
  return new Date(date).toISOString();
};

const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString("fr-FR", {
    year: "numeric",
    month: "long",
    day: "numeric",
  });
};
</script>

<template>
  <div class="py-6 sm:py-8">
    <div class="mx-auto max-w-4xl px-4 sm:px-6 lg:px-8">
      <!-- Navigation épurée -->
      <nav class="mb-8">
        <NuxtLink
          to="/conseil-des-ministres"
          class="group inline-flex items-center text-sm font-medium text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200"
        >
          <UIcon
            name="i-heroicons-chevron-left"
            class="mr-2 h-4 w-4 transition-transform duration-200 group-hover:-translate-x-1"
          />
          Retour aux communiqués
        </NuxtLink>
      </nav>

      <!-- Loading state épuré -->
      <div v-if="loading" class="space-y-6">
        <div class="space-y-4">
          <!-- Badge skeleton -->
          <div
            class="h-6 w-32 animate-pulse rounded-full bg-gray-200 dark:bg-gray-700"
          ></div>

          <!-- Titre skeleton -->
          <div class="space-y-3">
            <div
              class="h-8 w-3/4 animate-pulse rounded bg-gray-200 dark:bg-gray-700"
            ></div>
            <div
              class="h-8 w-1/2 animate-pulse rounded bg-gray-200 dark:bg-gray-700"
            ></div>
          </div>

          <!-- Meta skeleton -->
          <div class="flex items-center space-x-4">
            <div
              class="h-4 w-32 animate-pulse rounded bg-gray-200 dark:bg-gray-700"
            ></div>
            <div
              class="h-4 w-24 animate-pulse rounded bg-gray-200 dark:bg-gray-700"
            ></div>
          </div>
        </div>

        <!-- Image skeleton -->
        <div
          class="aspect-[16/9] w-full animate-pulse rounded-2xl bg-gray-200 dark:bg-gray-700"
        >
          <div class="skeleton-shimmer h-full w-full rounded-2xl"></div>
        </div>

        <!-- Content skeleton -->
        <div class="space-y-4">
          <div
            class="h-4 w-full animate-pulse rounded bg-gray-200 dark:bg-gray-700"
          ></div>
          <div
            class="h-4 w-5/6 animate-pulse rounded bg-gray-200 dark:bg-gray-700"
          ></div>
          <div
            class="h-4 w-4/5 animate-pulse rounded bg-gray-200 dark:bg-gray-700"
          ></div>
          <div
            class="h-4 w-full animate-pulse rounded bg-gray-200 dark:bg-gray-700"
          ></div>
          <div
            class="h-4 w-3/4 animate-pulse rounded bg-gray-200 dark:bg-gray-700"
          ></div>
        </div>
      </div>

      <!-- Error state moderne -->
      <div v-else-if="error" class="py-12 text-center">
        <div
          class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-red-100 dark:bg-red-900/30"
        >
          <UIcon
            name="i-heroicons-exclamation-triangle"
            class="h-8 w-8 text-red-600 dark:text-red-400"
          />
        </div>
        <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
          Erreur de chargement
        </h3>
        <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
          {{ error }}
        </p>
        <button
          class="mt-4 inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-900"
          @click="fetchNewsById(route.params.id as string)"
        >
          Réessayer
        </button>
      </div>

      <!-- Article content -->
      <article
        v-else-if="article"
        class="article-fade-in"
        itemscope
        itemtype="https://schema.org/GovernmentAnnouncement"
      >
        <!-- Schema.org metadata (gardées cachées) -->
        <div
          itemprop="publisher"
          itemscope
          itemtype="https://schema.org/GovernmentOrganization"
          class="hidden"
        >
          <meta itemprop="name" content="Conseil des ministres du Sénégal" />
          <meta itemprop="url" :content="`${siteUrl}/conseil-des-ministres`" />
        </div>

        <div
          itemprop="about"
          itemscope
          itemtype="https://schema.org/GovernmentOrganization"
          class="hidden"
        >
          <meta itemprop="name" content="Conseil des ministres du Sénégal" />

          <div
            itemprop="parentOrganization"
            itemscope
            itemtype="https://schema.org/GovernmentOrganization"
          >
            <meta itemprop="name" content="République du Sénégal" />
          </div>
        </div>

        <meta itemprop="url" :content="url" />
        <meta itemprop="genre" content="Communiqué officiel" />
        <meta itemprop="articleSection" content="Gouvernement" />

        <!-- Header de l'article -->
        <header class="mb-8">
          <!-- Badge gouvernemental -->
          <div class="mb-4">
            <span
              class="inline-flex items-center rounded-full bg-amber-50 px-3 py-1 text-sm font-medium text-amber-700 dark:bg-amber-900/20 dark:text-amber-400"
            >
              <div class="mr-2 h-2 w-2 rounded-full bg-amber-500" />
              Communiqué officiel
            </span>
          </div>

          <!-- Titre principal -->
          <h1
            class="text-3xl font-bold tracking-tight text-gray-900 sm:text-4xl lg:text-5xl dark:text-white"
            itemprop="headline name"
          >
            {{ article.title }}
          </h1>

          <!-- Métadonnées -->
          <div
            class="mt-6 flex items-center space-x-6 text-sm text-gray-500 dark:text-gray-400"
          >
            <div class="flex items-center">
              <UIcon name="i-heroicons-calendar-days" class="mr-2 h-4 w-4" />
              <time
                v-if="article.date_published"
                :datetime="formatDateISO(article.date_published)"
                itemprop="datePublished"
              >
                {{ $dateformatWithDayName(article.date_published) }}
              </time>
            </div>

            <div
              v-if="
                article.date_updated &&
                article.date_updated !== article.date_published
              "
              class="flex items-center"
            >
              <UIcon name="i-heroicons-pencil" class="mr-2 h-4 w-4" />
              <span>
                Mis à jour le {{ formatDate(article.date_updated) }}
              </span>
            </div>

            <div class="flex items-center">
              <UIcon
                name="i-heroicons-building-office-2"
                class="mr-2 h-4 w-4"
              />
              <span>Conseil des ministres</span>
            </div>
          </div>

          <!-- Metadata cachées -->
          <meta
            v-if="article.date_updated"
            itemprop="dateModified"
            :content="formatDateISO(article.date_updated)"
          />
        </header>

        <!-- Image principale -->
        <figure
          v-if="article.cover_image"
          itemprop="image"
          itemscope
          itemtype="https://schema.org/ImageObject"
          class="mb-8"
        >
          <div class="overflow-hidden rounded-2xl shadow-lg">
            <img
              :src="$directusImageUrl(article.cover_image, '100')"
              :alt="article.title"
              class="w-full object-cover"
              itemprop="contentUrl url"
            />
          </div>
          <meta itemprop="width" content="800" />
          <meta itemprop="height" content="450" />
        </figure>

        <!-- Document PDF -->
        <div v-if="article.document" class="mb-8">
          <div
            class="rounded-xl border border-gray-200 bg-gray-50 p-4 dark:border-gray-700 dark:bg-gray-800"
          >
            <div class="flex items-center space-x-3">
              <div
                class="flex h-10 w-10 items-center justify-center rounded-lg bg-red-100 dark:bg-red-900/30"
              >
                <UIcon
                  name="i-heroicons-document-arrow-down"
                  class="h-5 w-5 text-red-600 dark:text-red-400"
                />
              </div>
              <div class="flex-1">
                <h3 class="text-sm font-medium text-gray-900 dark:text-white">
                  Document PDF officiel
                </h3>
                <p class="text-sm text-gray-500 dark:text-gray-400">
                  Télécharger la version complète du communiqué
                </p>
              </div>
              <a
                :href="getAssetUrl(article.document.file, article.slug)"
                target="_blank"
                rel="noopener"
                class="inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-800"
              >
                <UIcon
                  name="i-heroicons-arrow-down-tray"
                  class="mr-2 h-4 w-4"
                />
                Télécharger
              </a>
            </div>
          </div>
        </div>

        <!-- Contenu principal -->
        <div
          class="prose prose-lg prose-amber prose-headings:text-gray-900 prose-headings:dark:text-white prose-p:text-gray-700 prose-p:dark:text-gray-300 prose-a:text-amber-600 prose-a:dark:text-amber-400 prose-strong:text-gray-900 prose-strong:dark:text-white prose-img:rounded-xl prose-img:mx-auto dark:prose-invert max-w-none"
          itemprop="articleBody"
          v-html="article.content"
        />

        <!-- Mots-clés cachés pour le SEO -->
        <meta
          itemprop="keywords"
          content="Conseil des ministres, Sénégal, Gouvernement, Communiqué officiel"
        />
      </article>

      <!-- Not found state -->
      <div v-else class="py-12 text-center">
        <div
          class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800"
        >
          <UIcon
            name="i-heroicons-document-text"
            class="h-8 w-8 text-gray-400"
          />
        </div>
        <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
          Communiqué non trouvé
        </h3>
        <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
          Ce communiqué n'existe pas ou a été supprimé
        </p>
      </div>

      <!-- Scroll to top (optionnel) -->
      <ScrollToTopButton />
    </div>
  </div>
</template>

<style scoped>
/* Animation d'apparition de l'article */
@keyframes articleFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.article-fade-in {
  animation: articleFadeIn 0.6s ease-out;
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

/* Amélioration du contenu prose */
.prose {
  line-height: 1.75;
}

.prose h2 {
  margin-top: 2em;
  margin-bottom: 1em;
  border-bottom: 1px solid rgba(251, 191, 36, 0.2);
  padding-bottom: 0.5em;
}

.prose h3 {
  margin-top: 1.6em;
  margin-bottom: 0.8em;
}

.prose p {
  margin-bottom: 1.5em;
}

/* Style spécifique pour les documents gouvernementaux */
.prose blockquote {
  border-left: 4px solid #f59e0b;
  background: rgba(251, 191, 36, 0.05);
  padding: 1rem 1.5rem;
  border-radius: 0.5rem;
}

.dark .prose blockquote {
  background: rgba(251, 191, 36, 0.1);
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .prose {
    font-size: 1rem;
    line-height: 1.6;
  }

  .prose h1 {
    font-size: 1.875rem;
  }

  .prose h2 {
    font-size: 1.5rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .article-fade-in {
    animation: none;
  }

  .skeleton-shimmer {
    animation: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.article-fade-in {
  will-change: opacity, transform;
}

/* Print styles */
@media print {
  .article-fade-in {
    animation: none;
  }

  nav {
    display: none;
  }

  .prose {
    max-width: none;
    font-size: 12pt;
    line-height: 1.5;
  }

  .prose h1 {
    color: #000;
    page-break-after: avoid;
  }

  .prose h2 {
    color: #000;
    page-break-after: avoid;
  }
}
</style>
