<script setup lang="ts">
import { useNews } from "~/composables/news/useNews";

const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();

const route = useRoute();
const config = useRuntimeConfig();
const { article, loading, error, fetchNewsById } = useNews();

const title = computed(() => {
  if (!article.value) return "Chargement...";
  return `${article.value.title} | Actualités Sénégal`;
});

const description = computed(() => {
  if (!article.value) return "";
  const plainText =
    article.value.content?.replace(/<[^>]*>/g, "") || article.value.title;
  const excerpt =
    plainText.length > 160 ? plainText.substring(0, 157) + "..." : plainText;
  return `${excerpt} Publié le ${formatDate(article.value.date_published)} - Actualités République du Sénégal.`;
});

const url = computed(() => {
  if (!route.params.id || !route.params.slug) return siteUrl;
  return `${siteUrl}/actualites/${route.params.id}/${route.params.slug}`;
});

const image = computed(() => {
  if (!article.value) return defaultImage;
  return article.value.cover_image
    ? `${config.public.cmsApiUrl}/assets/${article.value.cover_image}`
    : defaultImage;
});

const pdfUrl = computed(() => {
  if (!article.value?.document?.file) return "";
  return `${config.public.cmsApiUrl}/assets/${article.value.document.file}/${article.value.slug}.pdf`;
});

// Schemas SEO (gardés identiques)
const articleSchema = computed(() => {
  if (!article.value) return null;

  return {
    "@context": "https://schema.org",
    "@type": "NewsArticle",
    headline: article.value.title,
    description: description.value,
    image: {
      "@type": "ImageObject",
      url: image.value,
      width: 800,
      height: 450,
    },
    url: url.value,
    datePublished: formatDateISO(article.value.date_published),
    dateModified: article.value.date_updated
      ? formatDateISO(article.value.date_updated)
      : formatDateISO(article.value.date_published),
    author: {
      "@type": "Organization",
      name: siteName,
      url: siteUrl,
    },
    publisher: {
      "@type": "NewsMediaOrganization",
      name: siteName,
      url: siteUrl,
      logo: {
        "@type": "ImageObject",
        url: defaultImage,
      },
    },
    mainEntityOfPage: {
      "@type": "WebPage",
      "@id": url.value,
    },
    articleSection: article.value.category?.name || "Actualités",
    keywords:
      article.value.tags?.join(", ") || "République du Sénégal, actualités",
    about: {
      "@type": "GovernmentOrganization",
      name: "République du Sénégal",
    },
    isPartOf: {
      "@type": "WebSite",
      name: siteName,
      url: siteUrl,
    },
    inLanguage: "fr-SN",
  };
});

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
      name: "Actualités",
      item: `${siteUrl}/actualites`,
    },
    {
      "@type": "ListItem",
      position: 3,
      name: article.value?.title || "Article",
      item: url.value,
    },
  ],
}));

// Helper functions
const getImageUrl = (imageId: string) => {
  return `${config.public.cmsApiUrl}/assets/${imageId}`;
};

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

const getCategoryConfig = (categoryName: string) => {
  const configs = {
    "Conseil des ministres": {
      color: "bg-amber-500",
      textColor: "text-amber-700 dark:text-amber-400",
      bgColor: "bg-amber-50 dark:bg-amber-900/20",
    },
    "Assemblée nationale": {
      color: "bg-blue-500",
      textColor: "text-blue-700 dark:text-blue-400",
      bgColor: "bg-blue-50 dark:bg-blue-900/20",
    },
    Article: {
      color: "bg-emerald-500",
      textColor: "text-emerald-700 dark:text-emerald-400",
      bgColor: "bg-emerald-50 dark:bg-emerald-900/20",
    },
  };

  return (
    configs[categoryName as keyof typeof configs] || {
      color: "bg-gray-500",
      textColor: "text-gray-700 dark:text-gray-400",
      bgColor: "bg-gray-50 dark:bg-gray-900/20",
    }
  );
};

// SEO setup
watchEffect(() => {
  if (article.value) {
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
        ...(article.value.tags || []),
        "actualités République Sénégal",
        "news Sénégal",
        article.value.category?.name || "",
      ]
        .filter(Boolean)
        .join(", "),
    });

    useHead({
      htmlAttrs: { lang: "fr-SN" },
      link: [
        { rel: "canonical", href: url.value },
        article.value.document?.file
          ? {
              rel: "alternate",
              type: "application/pdf",
              href: pdfUrl.value,
            }
          : null,
      ].filter(Boolean),
      meta: [
        { name: "theme-color", content: themeColor },
        { name: "author", content: siteName },
        { property: "og:type", content: "article" },
        { property: "og:site_name", content: siteName },
        {
          property: "article:published_time",
          content: formatDateISO(article.value.date_published),
        },
        {
          property: "article:modified_time",
          content: article.value.date_updated
            ? formatDateISO(article.value.date_updated)
            : formatDateISO(article.value.date_published),
        },
        { property: "article:author", content: siteName },
        {
          property: "article:section",
          content: article.value.category?.name || "Actualités",
        },
        {
          property: "article:tag",
          content: article.value.tags?.join(", ") || "",
        },
        { name: "robots", content: "index, follow" },
        { name: "geo.region", content: "SN" },
        { name: "geo.placename", content: "Dakar" },
        { name: "geo.position", content: "14.7645042;-17.3660286" },
        { name: "ICBM", content: "14.7645042, -17.3660286" },
        {
          name: "news_keywords",
          content: article.value.tags?.join(", ") || "République du Sénégal",
        },
      ],
      script: [
        articleSchema.value
          ? {
              type: "application/ld+json",
              children: JSON.stringify(articleSchema.value),
            }
          : null,
        {
          type: "application/ld+json",
          children: JSON.stringify(breadcrumbSchema.value),
        },
      ].filter(Boolean),
    });
  }
});

// Chargement de l'article
onMounted(async () => {
  if (route.params.id) {
    await fetchNewsById(route.params.id as string);
  }
});
</script>

<template>
  <div class="py-6 sm:py-8">
    <div
      class="mx-auto max-w-4xl px-4 sm:px-6 lg:px-8"
      itemscope
      itemtype="https://schema.org/WebPage"
    >
      <!-- Bouton retour moderne -->
      <nav class="mb-8">
        <NuxtLink
          to="/actualites"
          class="group inline-flex items-center text-sm font-medium text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200"
        >
          <UIcon
            name="i-heroicons-chevron-left"
            class="mr-2 h-4 w-4 transition-transform duration-200 group-hover:-translate-x-1"
          />
          Retour aux actualités
        </NuxtLink>
      </nav>

      <!-- Loading state épuré -->
      <div v-if="loading" class="space-y-6">
        <div class="space-y-4">
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
        itemtype="https://schema.org/NewsArticle"
        itemprop="mainEntity"
      >
        <!-- Schema.org metadata (gardées identiques) -->
        <meta itemprop="url" :content="url" />
        <meta
          itemprop="datePublished"
          :content="formatDateISO(article.date_published)"
        />
        <meta
          itemprop="dateModified"
          :content="
            article.date_updated
              ? formatDateISO(article.date_updated)
              : formatDateISO(article.date_published)
          "
        />
        <meta
          itemprop="articleSection"
          :content="article.category?.name || 'Actualités'"
        />
        <meta
          itemprop="keywords"
          :content="article.tags?.join(', ') || 'République du Sénégal'"
        />
        <meta itemprop="inLanguage" content="fr-SN" />

        <div
          itemprop="publisher"
          itemscope
          itemtype="https://schema.org/NewsMediaOrganization"
        >
          <meta itemprop="name" :content="siteName" />
          <meta itemprop="url" :content="siteUrl" />
          <div
            itemprop="logo"
            itemscope
            itemtype="https://schema.org/ImageObject"
          >
            <meta itemprop="url" :content="defaultImage" />
          </div>
        </div>

        <div
          itemprop="author"
          itemscope
          itemtype="https://schema.org/Organization"
        >
          <meta itemprop="name" :content="siteName" />
          <meta itemprop="url" :content="siteUrl" />
        </div>

        <div
          itemprop="mainEntityOfPage"
          itemscope
          itemtype="https://schema.org/WebPage"
        >
          <meta itemprop="@id" :content="url" />
        </div>

        <!-- Header de l'article -->
        <header class="mb-8">
          <!-- Badge de catégorie -->
          <div v-if="article.category?.name" class="mb-4">
            <span
              class="inline-flex items-center rounded-full px-3 py-1 text-sm font-medium"
              :class="
                getCategoryConfig(article.category.name).bgColor +
                ' ' +
                getCategoryConfig(article.category.name).textColor
              "
            >
              <div
                class="mr-2 h-2 w-2 rounded-full"
                :class="getCategoryConfig(article.category.name).color"
              />
              {{ article.category.name }}
            </span>
          </div>

          <!-- Titre principal -->
          <h1
            class="text-3xl font-bold tracking-tight text-gray-900 sm:text-4xl lg:text-5xl dark:text-white"
            itemprop="headline"
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
                :datetime="formatDateISO(article.date_published)"
                itemprop="datePublished"
              >
                {{ formatDate(article.date_published) }}
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
          </div>
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
              :src="getImageUrl(article.cover_image)"
              :alt="article.title"
              class="w-full object-cover"
              loading="lazy"
              fetchpriority="high"
              itemprop="contentUrl"
            />
          </div>
          <meta itemprop="url" :content="getImageUrl(article.cover_image)" />
          <meta itemprop="width" content="800" />
          <meta itemprop="height" content="450" />
          <meta itemprop="caption" :content="article.title" />
        </figure>

        <!-- Document PDF -->
        <div v-if="article.document" class="mb-8">
          <div
            itemprop="associatedMedia"
            itemscope
            itemtype="https://schema.org/DigitalDocument"
            class="rounded-xl border border-gray-200 bg-gray-50 p-4 dark:border-gray-700 dark:bg-gray-800"
          >
            <meta itemprop="encodingFormat" content="application/pdf" />
            <meta itemprop="url" :content="pdfUrl" />
            <meta itemprop="isAccessibleForFree" content="true" />

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
                  Document PDF
                </h3>
                <p class="text-sm text-gray-500 dark:text-gray-400">
                  Télécharger la version complète
                </p>
              </div>
              <a
                :href="pdfUrl"
                target="_blank"
                class="inline-flex items-center rounded-lg bg-red-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 dark:focus:ring-offset-gray-800"
                itemprop="url"
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

        <!-- Tags -->
        <div v-if="article.tags?.length" class="mb-8">
          <div class="flex flex-wrap gap-2">
            <span
              v-for="tag in article.tags"
              :key="tag"
              class="inline-flex items-center rounded-full bg-blue-50 px-3 py-1 text-sm font-medium text-blue-700 dark:bg-blue-900/30 dark:text-blue-400"
              itemprop="keywords"
            >
              #{{ tag }}
            </span>
          </div>
        </div>

        <!-- Contenu principal -->
        <div
          class="prose prose-lg prose-blue prose-headings:text-gray-900 prose-headings:dark:text-white prose-p:text-gray-700 prose-p:dark:text-gray-300 prose-a:text-blue-600 prose-a:dark:text-blue-400 prose-strong:text-gray-900 prose-strong:dark:text-white prose-img:rounded-xl dark:prose-invert max-w-none"
          itemprop="articleBody"
          v-html="article.content"
        />

        <!-- About information (caché mais gardé pour le SEO) -->
        <div
          itemprop="about"
          itemscope
          itemtype="https://schema.org/GovernmentOrganization"
          class="hidden"
        >
          <meta itemprop="name" content="République du Sénégal" />
        </div>
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
          Article non trouvé
        </h3>
        <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
          Cet article n'existe pas ou a été supprimé
        </p>
      </div>
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
}

.prose h3 {
  margin-top: 1.6em;
  margin-bottom: 0.8em;
}

.prose p {
  margin-bottom: 1.5em;
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
}
</style>
