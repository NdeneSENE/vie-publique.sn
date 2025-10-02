<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } =
  useSiteMetadata();

const route = useRoute();
const config = useRuntimeConfig();
const { question, loading, error, fetchAssemblyQuestionById } =
  useAssemblyQuestions();

const questionFullName = computed(() => {
  if (!question.value) return "";
  return `${question.value.deputy.first_name} ${question.value.deputy.last_name}`;
});

const title = computed(() => {
  if (!question.value) return "Chargement...";
  return `${question.value.subject} | Question écrite de ${questionFullName.value}`;
});

const description = computed(() => {
  if (!question.value) return "";
  // Extraire du texte brut du contenu HTML
  const plainText =
    question.value.question_text?.replace(/<[^>]*>/g, "") ||
    question.value.subject;
  const excerpt =
    plainText.length > 160 ? plainText.substring(0, 157) + "..." : plainText;
  return `Question écrite posée par ${questionFullName.value} le ${formatDate(question.value.question_date)}. ${excerpt}`;
});

const url = computed(() => {
  if (!route.params.id) return siteUrl;
  return `${siteUrl}/assemblee-nationale/questions/${route.params.id}`;
});

const image = computed(() => {
  if (!question.value) return defaultImage;
  return question.value.deputy.photo
    ? `${config.public.cmsApiUrl}/assets/${question.value.deputy.photo}`
    : defaultImage;
});

const questionSchema = computed(() => {
  if (!question.value) return null;

  return {
    "@context": "https://schema.org",
    "@type": "Question",
    name: question.value.subject,
    text:
      question.value.question_text?.replace(/<[^>]*>/g, "") ||
      question.value.subject,
    dateCreated: formatDateISO(question.value.question_date),
    url: url.value,
    author: {
      "@type": "Person",
      name: questionFullName.value,
      givenName: question.value.deputy.first_name,
      familyName: question.value.deputy.last_name,
      jobTitle: "Député",
      image: question.value.deputy.photo
        ? `${config.public.cmsApiUrl}/assets/${question.value.deputy.photo}`
        : undefined,
      worksFor: {
        "@type": "GovernmentOrganization",
        name: "Assemblée nationale du Sénégal",
        url: `${siteUrl}/assemblee-nationale`,
      },
    },
    about: {
      "@type": "GovernmentOrganization",
      name: "Gouvernement du Sénégal",
    },
    isPartOf: {
      "@type": "CollectionPage",
      name: "Questions écrites parlementaires",
      url: `${siteUrl}/assemblee-nationale/questions`,
    },
    mainEntity: {
      "@type": "GovernmentOrganization",
      name: "Assemblée nationale du Sénégal",
    },
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
      name: "Assemblée nationale",
      item: `${siteUrl}/assemblee-nationale`,
    },
    {
      "@type": "ListItem",
      position: 3,
      name: "Questions écrites",
      item: `${siteUrl}/assemblee-nationale/questions`,
    },
    {
      "@type": "ListItem",
      position: 4,
      name: question.value?.subject || "Question",
      item: url.value,
    },
  ],
}));

const webPageSchema = computed(() => {
  if (!question.value) return null;

  return {
    "@context": "https://schema.org",
    "@type": "WebPage",
    name: title.value,
    description: description.value,
    url: url.value,
    image: image.value,
    isPartOf: {
      "@type": "WebSite",
      name: siteName,
      url: siteUrl,
    },
    about: {
      "@type": "GovernmentOrganization",
      name: "Assemblée nationale du Sénégal",
    },
    mainEntity: questionSchema.value,
  };
});

// Helper functions
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

const getImageUrl = (imageId: string) => {
  return `${config.public.cmsApiUrl}/assets/${imageId}`;
};

const isImageFile = (fileType: string) => {
  return fileType.startsWith("image/");
};

watchEffect(() => {
  if (question.value) {
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
        `${questionFullName.value}`,
        "question écrite",
        "député Sénégal",
        "Assemblée nationale Sénégal",
        "contrôle parlementaire",
        question.value.subject,
      ]
        .filter(Boolean)
        .join(", "),
    });

    // Head Configuration
    useHead({
      htmlAttrs: { lang: "fr-SN" },
      link: [{ rel: "canonical", href: url.value }],
      meta: [
        { name: "theme-color", content: themeColor },
        { name: "author", content: questionFullName.value },
        { property: "og:type", content: "article" },
        { property: "og:site_name", content: siteName },
        {
          property: "article:published_time",
          content: formatDateISO(question.value.question_date),
        },
        { property: "article:author", content: questionFullName.value },
        { property: "article:section", content: "Questions parlementaires" },
        { name: "robots", content: "index, follow" },
        { name: "geo.region", content: "SN" },
        { name: "geo.placename", content: "Dakar" },
        { name: "geo.position", content: "14.7645042;-17.3660286" },
        { name: "ICBM", content: "14.7645042, -17.3660286" },
      ],
      script: [
        questionSchema.value
          ? {
              type: "application/ld+json",
              children: JSON.stringify(questionSchema.value),
            }
          : null,
        {
          type: "application/ld+json",
          children: JSON.stringify(breadcrumbSchema.value),
        },
        webPageSchema.value
          ? {
              type: "application/ld+json",
              children: JSON.stringify(webPageSchema.value),
            }
          : null,
      ].filter(Boolean),
    });
  }
});

onMounted(async () => {
  if (route.params.id) {
    await fetchAssemblyQuestionById(route.params.id as string);
  }
});
</script>

<template>
  <div
    class="min-h-screen bg-gray-50 dark:bg-gray-900"
    itemscope
    itemtype="https://schema.org/WebPage"
  >
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
              <NuxtLink to="/assemblee-nationale" class="text-sm font-medium text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                Assemblée nationale
              </NuxtLink>
            </li>
            <UIcon name="i-heroicons-chevron-right" class="h-4 w-4 text-gray-300 dark:text-gray-600" />
            <li>
              <NuxtLink to="/assemblee-nationale/questions" class="text-sm font-medium text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                Questions écrites
              </NuxtLink>
            </li>
            <UIcon name="i-heroicons-chevron-right" class="h-4 w-4 text-gray-300 dark:text-gray-600" />
            <li>
              <span class="text-sm font-medium text-blue-600 dark:text-blue-400" aria-current="page">
                {{ question?.subject || 'Question' }}
              </span>
            </li>
          </ol>
        </div>
      </div>
    </nav>

    <div class="mx-auto max-w-7xl px-4 py-8 sm:px-6 lg:px-8">
      <div class="mx-auto max-w-4xl">

        <!-- État de chargement -->
        <div v-if="loading" class="space-y-6">
          <div class="animate-pulse">
            <!-- Header skeleton -->
            <div class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
              <div class="flex items-center space-x-4">
                <div class="h-20 w-20 rounded-full bg-gray-200 dark:bg-gray-700"></div>
                <div class="flex-1">
                  <div class="mb-2 h-6 w-3/4 rounded bg-gray-200 dark:bg-gray-700"></div>
                  <div class="h-4 w-1/2 rounded bg-gray-200 dark:bg-gray-700"></div>
                </div>
              </div>
              <div class="mt-6 space-y-2">
                <div class="h-8 w-full rounded bg-gray-200 dark:bg-gray-700"></div>
                <div class="h-4 w-full rounded bg-gray-200 dark:bg-gray-700"></div>
                <div class="h-4 w-3/4 rounded bg-gray-200 dark:bg-gray-700"></div>
              </div>
            </div>
          </div>
        </div>

        <!-- État d'erreur -->
        <div v-else-if="error" class="text-center py-12">
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-red-100 dark:bg-red-900/50">
            <UIcon name="i-heroicons-exclamation-triangle" class="h-8 w-8 text-red-600 dark:text-red-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Erreur de chargement
          </h3>
          <p class="mt-2 text-gray-600 dark:text-gray-400">
            {{ error }}
          </p>
          <UButton
            class="mt-4"
            variant="outline"
            @click="fetchAssemblyQuestionById(route.params.id as string)"
          >
            Réessayer
          </UButton>
        </div>

        <!-- Contenu de la question -->
        <div v-else-if="question" class="space-y-6">
          <!-- Schema.org hidden metadata -->
          <div
            itemscope
            itemtype="https://schema.org/Question"
            itemprop="mainEntity"
          >
            <meta itemprop="url" :content="url" />
            <meta
              itemprop="dateCreated"
              :content="formatDateISO(question.question_date)"
            />
            <meta itemprop="name" :content="question.subject" />

            <!-- En-tête avec info député -->
            <div class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
              <div
                itemprop="author"
                itemscope
                itemtype="https://schema.org/Person"
              >
                <meta itemprop="name" :content="questionFullName" />
                <meta
                  itemprop="givenName"
                  :content="question.deputy.first_name"
                />
                <meta
                  itemprop="familyName"
                  :content="question.deputy.last_name"
                />
                <meta itemprop="jobTitle" content="Député" />
                <meta
                  itemprop="image"
                  :content="getImageUrl(question.deputy.photo)"
                />

                <div
                  itemprop="worksFor"
                  itemscope
                  itemtype="https://schema.org/GovernmentOrganization"
                >
                  <meta
                    itemprop="name"
                    content="Assemblée nationale du Sénégal"
                  />
                  <meta
                    itemprop="url"
                    :content="`${siteUrl}/assemblee-nationale`"
                  />
                </div>

                <!-- Informations du député -->
                <div class="mb-8 flex items-start space-x-6">
                  <div class="flex-shrink-0">
                    <img
                      :src="getImageUrl(question.deputy.photo)"
                      :alt="question.deputy.first_name"
                      class="h-24 w-24 rounded-full object-cover ring-4 ring-white dark:ring-gray-700"
                      itemprop="image"
                    />
                  </div>
                  <div class="flex-1 min-w-0">
                    <div class="mb-2">
                      <UBadge color="blue" variant="subtle" size="sm">
                        <UIcon name="i-heroicons-user" class="mr-1 h-3 w-3" />
                        Député
                      </UBadge>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-900 dark:text-white">
                      <span itemprop="givenName">{{ question.deputy.first_name }}</span>
                      <span itemprop="familyName">{{ question.deputy.last_name }}</span>
                    </h2>
                    <div class="mt-2 flex items-center space-x-4 text-sm text-gray-500 dark:text-gray-400">
                      <div class="flex items-center">
                        <UIcon name="i-heroicons-calendar" class="mr-1 h-4 w-4" />
                        <time
                          :datetime="formatDateISO(question.question_date)"
                          itemprop="dateCreated"
                        >
                          {{ formatDate(question.question_date) }}
                        </time>
                      </div>
                    </div>
                    <div class="mt-4">
                      <NuxtLink
                        :to="`/assemblee-nationale/deputes/${question.deputy.id}/${$getSlugifyUrlPath(question.deputy.first_name + ' ' + question.deputy.last_name)}`"
                        class="inline-flex items-center text-blue-600 hover:text-blue-700 dark:text-blue-400 dark:hover:text-blue-300"
                        itemprop="url"
                      >
                        Voir son profil
                        <UIcon name="i-heroicons-arrow-top-right-on-square" class="ml-1 h-4 w-4" />
                      </NuxtLink>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Sujet de la question -->
              <div class="border-t border-gray-200 pt-6 dark:border-gray-700">
                <h1
                  class="text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl"
                  itemprop="name"
                >
                  {{ question.subject }}
                </h1>
              </div>

              <!-- Corps de la question -->
              <div class="mt-8">
                <div class="rounded-lg bg-gray-50 p-6 dark:bg-gray-700/50">
                  <h3 class="mb-4 text-lg font-semibold text-gray-900 dark:text-white">
                    Texte de la question
                  </h3>
                  <div
                    class="prose prose-gray dark:prose-invert max-w-none"
                    itemprop="text"
                    v-html="question.question_text"
                  ></div>
                </div>
              </div>
            </div>

            <!-- About information -->
            <div
              itemprop="about"
              itemscope
              itemtype="https://schema.org/GovernmentOrganization"
            >
              <meta itemprop="name" content="Gouvernement du Sénégal" />
            </div>

            <!-- Part of collection -->
            <div
              itemprop="isPartOf"
              itemscope
              itemtype="https://schema.org/CollectionPage"
            >
              <meta itemprop="name" content="Questions écrites parlementaires" />
              <meta
                itemprop="url"
                :content="`${siteUrl}/assemblee-nationale/questions`"
              />
            </div>
          </div>

          <!-- Pièces jointes -->
          <div
            v-if="question.attachments?.length > 0"
            class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800"
          >
            <h3 class="mb-6 text-xl font-semibold text-gray-900 dark:text-white">
              <UIcon name="i-heroicons-paper-clip" class="mr-2 h-5 w-5 inline" />
              Documents joints
            </h3>
            <div class="grid grid-cols-1 gap-4 sm:grid-cols-2">
              <div
                v-for="attachment in question.attachments"
                :key="attachment.directus_files_id.id"
                class="overflow-hidden rounded-lg border border-gray-200 bg-gray-50 p-4 dark:border-gray-700 dark:bg-gray-700/50"
                itemscope
                itemtype="https://schema.org/MediaObject"
              >
                <meta
                  itemprop="contentUrl"
                  :content="getImageUrl(attachment.directus_files_id.id)"
                />
                <meta
                  itemprop="encodingFormat"
                  :content="attachment.directus_files_id.type"
                />

                <!-- Image attachments -->
                <img
                  v-if="isImageFile(attachment.directus_files_id.type)"
                  :src="getImageUrl(attachment.directus_files_id.id)"
                  :alt="'Document joint'"
                  class="h-auto w-full rounded border border-gray-200 dark:border-gray-600"
                  itemprop="contentUrl"
                />
                <!-- Non-image attachments -->
                <UButton
                  v-else
                  :href="getImageUrl(attachment.directus_files_id.id)"
                  target="_blank"
                  variant="outline"
                  class="w-full"
                  external
                >
                  <UIcon name="i-heroicons-document" class="mr-2 h-5 w-5" />
                  Télécharger le document
                </UButton>
              </div>
            </div>
          </div>
        </div>

        <!-- État non trouvé -->
        <div v-else class="text-center py-12">
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-document-text" class="h-8 w-8 text-gray-400" />
          </div>
          <h3 class="mt-4 text-lg font-medium text-gray-900 dark:text-white">
            Question non trouvée
          </h3>
          <p class="mt-2 text-gray-600 dark:text-gray-400">
            La question demandée n'existe pas ou n'est plus disponible.
          </p>
          <NuxtLink to="/assemblee-nationale/questions">
            <UButton class="mt-4" variant="outline">
              Retour aux questions écrites
            </UButton>
          </NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>
