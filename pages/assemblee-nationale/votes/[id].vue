<!-- pages/assemblee-nationale/votes/[id].vue -->
<template>
  <div class="min-h-screen bg-gray-50 dark:bg-gray-900">
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
              <NuxtLink to="/assemblee-nationale/votes" class="text-sm font-medium text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                Votes
              </NuxtLink>
            </li>
            <UIcon name="i-heroicons-chevron-right" class="h-4 w-4 text-gray-300 dark:text-gray-600" />
            <li>
              <span class="text-sm font-medium text-blue-600 dark:text-blue-400" aria-current="page">
                Vote n° {{ vote?.id || route.params.id }}
              </span>
            </li>
          </ol>
        </div>
      </div>
    </nav>

    <div class="mx-auto max-w-7xl px-4 py-8 sm:px-6 lg:px-8">
      <!-- État de chargement -->
      <div v-if="loading" class="space-y-6">
        <div class="animate-pulse">
          <!-- Header skeleton -->
          <div class="mb-6 rounded-xl bg-white p-6 shadow-sm dark:bg-gray-800">
            <div class="mb-4 h-8 w-3/4 rounded bg-gray-200 dark:bg-gray-700"></div>
            <div class="mb-2 h-4 w-1/2 rounded bg-gray-200 dark:bg-gray-700"></div>
            <div class="h-6 w-24 rounded bg-gray-200 dark:bg-gray-700"></div>
          </div>
          <!-- Results skeleton -->
          <div class="rounded-xl bg-white p-6 shadow-sm dark:bg-gray-800">
            <div class="mb-4 grid grid-cols-3 gap-4">
              <div class="h-20 rounded-lg bg-gray-200 dark:bg-gray-700"></div>
              <div class="h-20 rounded-lg bg-gray-200 dark:bg-gray-700"></div>
              <div class="h-20 rounded-lg bg-gray-200 dark:bg-gray-700"></div>
            </div>
            <div class="space-y-2">
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
          @click="fetchAssemblyVoteById(route.params.id as string)"
        >
          Réessayer
        </UButton>
      </div>

      <!-- Contenu principal -->
      <div v-else-if="vote" class="space-y-6">
        <!-- Header Section -->
        <div class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
          <div class="flex items-start justify-between">
            <div class="flex-1">
              <div class="mb-2 flex items-center space-x-2">
                <UBadge color="gray" variant="subtle">
                  VOTE N° {{ vote.id }}
                </UBadge>
                <UBadge color="blue" variant="subtle">
                  {{ formatDate(vote.date) }}
                </UBadge>
              </div>
              <h1 class="text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl">
                {{ vote.name }}
              </h1>
            </div>
            <div class="ml-4">
              <UBadge
                :color="vote.status === 'adopted' ? 'emerald' : 'red'"
                size="lg"
                class="text-base font-semibold"
              >
                <UIcon
                  :name="vote.status === 'adopted' ? 'i-heroicons-check-circle' : 'i-heroicons-x-circle'"
                  class="mr-1.5 h-4 w-4"
                />
                {{ vote.status === "adopted" ? "ADOPTÉ" : "REJETÉ" }}
              </UBadge>
            </div>
          </div>
        </div>

        <!-- Résultats du vote -->
        <div v-if="vote?.voters_for !== undefined" class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
          <h2 class="mb-6 text-xl font-semibold text-gray-900 dark:text-white">
            Résultats du scrutin
          </h2>

          <!-- Statistiques en grille -->
          <div class="mb-6 grid grid-cols-1 gap-4 sm:grid-cols-3">
            <div class="rounded-lg bg-emerald-50 p-6 dark:bg-emerald-900/20">
              <div class="flex items-center">
                <div class="flex h-12 w-12 items-center justify-center rounded-lg bg-emerald-500">
                  <UIcon name="i-heroicons-hand-thumb-up" class="h-6 w-6 text-white" />
                </div>
                <div class="ml-4">
                  <p class="text-sm font-medium text-emerald-600 dark:text-emerald-400">POUR</p>
                  <p class="text-2xl font-bold text-emerald-900 dark:text-emerald-100">
                    {{ vote.voters_for ?? 0 }}
                  </p>
                </div>
              </div>
            </div>

            <div class="rounded-lg bg-amber-50 p-6 dark:bg-amber-900/20">
              <div class="flex items-center">
                <div class="flex h-12 w-12 items-center justify-center rounded-lg bg-amber-500">
                  <UIcon name="i-heroicons-hand-raised" class="h-6 w-6 text-white" />
                </div>
                <div class="ml-4">
                  <p class="text-sm font-medium text-amber-600 dark:text-amber-400">ABSTENTION</p>
                  <p class="text-2xl font-bold text-amber-900 dark:text-amber-100">
                    {{ vote.voters_abstention ?? 0 }}
                  </p>
                </div>
              </div>
            </div>

            <div class="rounded-lg bg-red-50 p-6 dark:bg-red-900/20">
              <div class="flex items-center">
                <div class="flex h-12 w-12 items-center justify-center rounded-lg bg-red-500">
                  <UIcon name="i-heroicons-hand-thumb-down" class="h-6 w-6 text-white" />
                </div>
                <div class="ml-4">
                  <p class="text-sm font-medium text-red-600 dark:text-red-400">CONTRE</p>
                  <p class="text-2xl font-bold text-red-900 dark:text-red-100">
                    {{ vote.voters_against ?? 0 }}
                  </p>
                </div>
              </div>
            </div>
          </div>

          <!-- Barre de progression visuelle -->
          <div class="overflow-hidden rounded-lg">
            <div class="flex h-4">
              <div
                class="bg-emerald-500"
                :style="{ width: `${getVotePercentage('for')}%` }"
                :title="`Pour: ${getVotePercentage('for')}%`"
              ></div>
              <div
                class="bg-amber-400"
                :style="{ width: `${getVotePercentage('abstention')}%` }"
                :title="`Abstention: ${getVotePercentage('abstention')}%`"
              ></div>
              <div
                class="bg-red-500"
                :style="{ width: `${getVotePercentage('against')}%` }"
                :title="`Contre: ${getVotePercentage('against')}%`"
              ></div>
            </div>
          </div>
        </div>

        <!-- Détails et description -->
        <div class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
          <h2 class="mb-6 text-xl font-semibold text-gray-900 dark:text-white">
            Détails du vote
          </h2>

          <div class="space-y-4">
            <div class="rounded-lg bg-gray-50 p-4 dark:bg-gray-700/50">
              <p class="text-gray-900 dark:text-gray-100">
                Les députés ont
                <span :class="[
                  'font-semibold',
                  vote.status === 'adopted' ? 'text-emerald-600 dark:text-emerald-400' : 'text-red-600 dark:text-red-400'
                ]">
                  {{ vote.status === "adopted" ? "adopté" : "rejeté" }}
                </span>
                cette proposition le {{ formatDate(vote.date) }}.
              </p>
            </div>

            <!-- Description si disponible -->
            <div
              v-if="vote.desc"
              class="prose prose-gray dark:prose-invert max-w-none"
              v-html="vote.desc"
            ></div>

            <!-- Statistiques détaillées -->
            <div v-if="getTotalVoters() > 0" class="mt-6 pt-6 border-t border-gray-200 dark:border-gray-700">
              <h3 class="text-lg font-medium text-gray-900 dark:text-white mb-4">Participation</h3>
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                <div class="flex justify-between">
                  <span class="text-gray-600 dark:text-gray-400">Total des votants:</span>
                  <span class="font-medium text-gray-900 dark:text-white">{{ getTotalVoters() }} députés</span>
                </div>
                <div class="flex justify-between">
                  <span class="text-gray-600 dark:text-gray-400">Taux de participation:</span>
                  <span class="font-medium text-gray-900 dark:text-white">{{ Math.round((getTotalVoters() / 165) * 100) }}%</span>
                </div>
              </div>
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
          Vote non trouvé
        </h3>
        <p class="mt-2 text-gray-600 dark:text-gray-400">
          Le vote demandé n'existe pas ou n'est plus disponible.
        </p>
        <NuxtLink to="/assemblee-nationale/votes">
          <UButton class="mt-4" variant="outline">
            Retour à la liste des votes
          </UButton>
        </NuxtLink>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const route = useRoute();
const router = useRouter();
const { fetchAssemblyVoteById, vote, loading, error } = useAssemblyVotes();

// SEO et métadonnées
const title = computed(() => {
  if (!vote.value) return "Chargement...";
  return `Vote n° ${vote.value.id}: ${vote.value.name} | Assemblée nationale Sénégal`;
});

const description = computed(() => {
  if (!vote.value) return "";
  const status = vote.value.status === 'adopted' ? 'adopté' : 'rejeté';
  return `Vote parlementaire ${status} le ${formatDate(vote.value.date)}. Résultats: ${vote.value.voters_for || 0} pour, ${vote.value.voters_against || 0} contre, ${vote.value.voters_abstention || 0} abstentions.`;
});

const url = computed(() => {
  if (!route.params.id) return siteUrl;
  return `${siteUrl}/assemblee-nationale/votes/${route.params.id}`;
});

watchEffect(() => {
  if (vote.value) {
    useSeoMeta({
      title: title.value,
      ogTitle: title.value,
      description: description.value,
      ogDescription: description.value,
      ogImage: defaultImage,
      ogUrl: url.value,
      twitterCard: "summary_large_image",
      twitterTitle: title.value,
      twitterDescription: description.value,
      twitterImage: defaultImage,
      keywords: [
        ...keywords,
        `vote ${vote.value.id}`,
        "assemblée nationale vote",
        "scrutin parlementaire Sénégal",
        vote.value.status === 'adopted' ? 'adopté' : 'rejeté',
      ].join(", "),
    });

    useHead({
      htmlAttrs: { lang: "fr-SN" },
      link: [{ rel: "canonical", href: url.value }],
      meta: [
        { name: "theme-color", content: themeColor },
        { name: "author", content: "Assemblée nationale du Sénégal" },
        { property: "og:type", content: "article" },
        { property: "og:site_name", content: siteName },
        { name: "robots", content: "index, follow" },
      ],
    });
  }
});

onMounted(() => {
  fetchAssemblyVoteById(route.params.id as string);
});

// Fonctions utilitaires
const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString("fr-FR", {
    day: "numeric",
    month: "long",
    year: "numeric",
  });
};

const getTotalVoters = () => {
  if (!vote.value) return 0;
  return (vote.value.voters_for || 0) + (vote.value.voters_against || 0) + (vote.value.voters_abstention || 0);
};

const getVotePercentage = (type: 'for' | 'against' | 'abstention') => {
  if (!vote.value) return 0;
  const total = getTotalVoters();
  if (total === 0) return 0;

  let count = 0;
  switch (type) {
    case 'for':
      count = vote.value.voters_for || 0;
      break;
    case 'against':
      count = vote.value.voters_against || 0;
      break;
    case 'abstention':
      count = vote.value.voters_abstention || 0;
      break;
  }

  return Math.round((count / total) * 100);
};
</script>
