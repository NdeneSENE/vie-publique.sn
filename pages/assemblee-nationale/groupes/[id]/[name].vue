<!-- pages/assemblee-nationale/groupes/[id]/[name].vue -->
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
              <NuxtLink to="/assemblee-nationale/groupes" class="text-sm font-medium text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                Groupes parlementaires
              </NuxtLink>
            </li>
            <UIcon name="i-heroicons-chevron-right" class="h-4 w-4 text-gray-300 dark:text-gray-600" />
            <li>
              <span class="text-sm font-medium text-blue-600 dark:text-blue-400" aria-current="page">
                {{ groupById?.name || 'Groupe' }}
              </span>
            </li>
          </ol>
        </div>
      </div>
    </nav>

    <!-- Message d'erreur -->
    <div v-if="error" class="mx-auto max-w-7xl px-4 py-8 sm:px-6 lg:px-8">
      <div class="text-center py-12">
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
          @click="fetchAssemblyGroupById(groupId)"
        >
          Réessayer
        </UButton>
      </div>
    </div>

    <!-- Contenu principal -->
    <div v-else class="mx-auto max-w-7xl px-4 py-8 sm:px-6 lg:px-8">
      <div class="flex flex-col gap-8 lg:flex-row">
        <!-- Sidebar -->
        <div class="lg:w-80">
          <div class="sticky top-8">
            <!-- État de chargement -->
            <div v-if="loading" class="space-y-6">
              <div class="animate-pulse">
                <div class="overflow-hidden rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
                  <div class="flex flex-col items-center">
                    <div class="mb-6 h-32 w-32 rounded-lg bg-gray-200 dark:bg-gray-700"></div>
                    <div class="mb-6 h-8 w-48 rounded bg-gray-200 dark:bg-gray-700"></div>
                    <div class="w-full space-y-4">
                      <div v-for="i in 3" :key="i" class="flex justify-between">
                        <div class="h-5 w-24 rounded bg-gray-200 dark:bg-gray-700"></div>
                        <div class="h-5 w-32 rounded bg-gray-200 dark:bg-gray-700"></div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Informations du groupe -->
            <div v-else-if="groupById" class="overflow-hidden rounded-xl bg-white shadow-sm dark:bg-gray-800">
              <div class="p-8">
                <div class="flex flex-col items-center">
                  <!-- Logo -->
                  <div class="mb-6">
                    <div v-if="groupById.logo" class="relative">
                      <img
                        :src="$directusImageUrl(groupById.logo, '150')"
                        :alt="`Logo ${groupById.name}`"
                        class="h-32 w-32 object-contain rounded-lg bg-white p-2 shadow-sm"
                        loading="lazy"
                        fetchpriority="high"
                      />
                    </div>
                    <div
                      v-else
                      class="flex h-32 w-32 items-center justify-center rounded-lg bg-gray-100 dark:bg-gray-700"
                    >
                      <UIcon
                        name="i-heroicons-user-group"
                        class="h-16 w-16 text-gray-400"
                      />
                    </div>
                  </div>

                  <!-- Nom du groupe -->
                  <h1 class="mb-6 text-center text-2xl font-bold text-gray-900 dark:text-white">
                    {{ groupById.name }}
                  </h1>

                  <!-- Informations détaillées -->
                  <div class="w-full space-y-6">
                    <!-- Statut -->
                    <div v-if="groupById.status === 'active'" class="text-center">
                      <UBadge color="emerald" size="lg" class="font-medium">
                        <UIcon name="i-heroicons-check-circle" class="mr-1.5 h-4 w-4" />
                        EN ACTIVITÉ
                      </UBadge>
                    </div>

                    <!-- Date de création -->
                    <div class="flex items-center justify-between border-b border-gray-200 pb-3 dark:border-gray-700">
                      <span class="text-sm text-gray-600 dark:text-gray-400">Création</span>
                      <span class="font-medium text-gray-900 dark:text-white">
                        {{ groupById.creation_date ? formatDate(groupById.creation_date) : "N/A" }}
                      </span>
                    </div>

                    <!-- Président -->
                    <div v-if="groupById.president" class="flex items-center justify-between border-b border-gray-200 pb-3 dark:border-gray-700">
                      <span class="text-sm text-gray-600 dark:text-gray-400">Président(e)</span>
                      <span class="font-medium text-gray-900 dark:text-white text-right">
                        {{ groupById.president.first_name }} {{ groupById.president.last_name }}
                      </span>
                    </div>

                    <!-- Vice-président -->
                    <div v-if="groupById.vice_president" class="flex items-center justify-between border-b border-gray-200 pb-3 dark:border-gray-700">
                      <span class="text-sm text-gray-600 dark:text-gray-400">Vice-président(e)</span>
                      <span class="font-medium text-gray-900 dark:text-white text-right">
                        {{ groupById.vice_president.first_name }} {{ groupById.vice_president.last_name }}
                      </span>
                    </div>

                    <!-- Nombre de députés -->
                    <div class="flex items-center justify-between">
                      <span class="text-sm text-gray-600 dark:text-gray-400">Députés</span>
                      <UBadge color="blue" variant="subtle">
                        {{ deputies?.length || 0 }} membres
                      </UBadge>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Contenu principal -->
        <div class="flex-1">
          <!-- État de chargement -->
          <div v-if="loading" class="space-y-6">
            <div class="animate-pulse">
              <div class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
                <div class="mb-4 h-8 w-64 rounded bg-gray-200 dark:bg-gray-700"></div>
                <div class="mb-3 h-4 w-full rounded bg-gray-200 dark:bg-gray-700"></div>
                <div class="mb-3 h-4 w-full rounded bg-gray-200 dark:bg-gray-700"></div>
                <div class="h-4 w-3/4 rounded bg-gray-200 dark:bg-gray-700"></div>
              </div>
              <div class="grid grid-cols-2 gap-4 sm:grid-cols-3 lg:grid-cols-4">
                <div v-for="i in 8" :key="i" class="h-32 rounded-xl bg-gray-200 dark:bg-gray-700"></div>
              </div>
            </div>
          </div>

          <!-- Contenu du groupe -->
          <div v-else-if="groupById" class="space-y-8">
            <!-- Description -->
            <div class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
              <h2 class="mb-6 text-xl font-semibold text-gray-900 dark:text-white">
                À propos du groupe
              </h2>
              <div v-if="groupById.description" class="prose prose-gray dark:prose-invert max-w-none">
                <p>{{ groupById.description }}</p>
              </div>
              <div v-else class="text-gray-600 dark:text-gray-400">
                <p>Aucune description disponible pour ce groupe parlementaire.</p>
              </div>

              <!-- Statistiques -->
              <div class="mt-8 grid grid-cols-1 gap-4 sm:grid-cols-3">
                <div class="rounded-lg bg-blue-50 p-4 dark:bg-blue-900/20">
                  <div class="flex items-center">
                    <UIcon name="i-heroicons-users" class="h-6 w-6 text-blue-600 dark:text-blue-400" />
                    <div class="ml-3">
                      <p class="text-sm font-medium text-blue-600 dark:text-blue-400">Députés</p>
                      <p class="text-lg font-bold text-blue-900 dark:text-blue-100">{{ deputies?.length || 0 }}</p>
                    </div>
                  </div>
                </div>

                <div v-if="groupById.creation_date" class="rounded-lg bg-green-50 p-4 dark:bg-green-900/20">
                  <div class="flex items-center">
                    <UIcon name="i-heroicons-calendar" class="h-6 w-6 text-green-600 dark:text-green-400" />
                    <div class="ml-3">
                      <p class="text-sm font-medium text-green-600 dark:text-green-400">Créé en</p>
                      <p class="text-lg font-bold text-green-900 dark:text-green-100">{{ new Date(groupById.creation_date).getFullYear() }}</p>
                    </div>
                  </div>
                </div>

                <div class="rounded-lg bg-purple-50 p-4 dark:bg-purple-900/20">
                  <div class="flex items-center">
                    <UIcon name="i-heroicons-check-circle" class="h-6 w-6 text-purple-600 dark:text-purple-400" />
                    <div class="ml-3">
                      <p class="text-sm font-medium text-purple-600 dark:text-purple-400">Statut</p>
                      <p class="text-lg font-bold text-purple-900 dark:text-purple-100">
                        {{ groupById.status === 'active' ? 'Actif' : 'Inactif' }}
                      </p>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- Membres du groupe -->
            <div class="rounded-xl bg-white p-8 shadow-sm dark:bg-gray-800">
              <div class="mb-6 flex items-center justify-between">
                <h2 class="text-xl font-semibold text-gray-900 dark:text-white">
                  Membres du groupe
                </h2>
                <UBadge color="blue" variant="subtle">
                  {{ deputies?.length || 0 }} députés
                </UBadge>
              </div>

              <div v-if="deputies?.length > 0" class="grid grid-cols-2 gap-4 sm:grid-cols-3 lg:grid-cols-4">
                <AssemblyDeputyCard2
                  v-for="deputy in deputies"
                  :key="deputy.id"
                  :deputy="deputy"
                />
              </div>

              <div v-else class="text-center py-8">
                <UIcon name="i-heroicons-users" class="mx-auto h-12 w-12 text-gray-400" />
                <p class="mt-2 text-gray-600 dark:text-gray-400">Aucun député membre de ce groupe</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useDeputev2 } from "@/composables/parliament/useDeputev2";

const { siteName, siteUrl, defaultImage, keywords, themeColor } = useSiteMetadata();

const route = useRoute();
const { fetchAssemblyGroupById, groupById, loading, error } = useAssemblyGroups();
const { deputies, fetchElectedDeputies } = useDeputev2();

const groupId = route.params.id as string;

// SEO et métadonnées
const title = computed(() => {
  if (!groupById.value) return "Chargement...";
  return `${groupById.value.name} | Groupe parlementaire Assemblée nationale Sénégal`;
});

const description = computed(() => {
  if (!groupById.value) return "";
  const presidentText = groupById.value.president
    ? ` Présidé par ${groupById.value.president.first_name} ${groupById.value.president.last_name}.`
    : "";
  const membersCount = deputies.value?.length || 0;
  return `${groupById.value.description || groupById.value.name}${presidentText} Groupe composé de ${membersCount} députés de l'Assemblée nationale du Sénégal.`;
});

const url = computed(() => {
  if (!route.params.id) return siteUrl;
  return `${siteUrl}/assemblee-nationale/groupes/${route.params.id}/${route.params.name || ''}`;
});

watchEffect(() => {
  if (groupById.value) {
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
        groupById.value.name,
        "groupe parlementaire Sénégal",
        "assemblée nationale groupe",
        groupById.value.president
          ? `${groupById.value.president.first_name} ${groupById.value.president.last_name}`
          : "",
        "députés groupe",
      ]
        .filter(Boolean)
        .join(", "),
    });

    useHead({
      htmlAttrs: { lang: "fr-SN" },
      link: [{ rel: "canonical", href: url.value }],
      meta: [
        { name: "theme-color", content: themeColor },
        { name: "author", content: "Assemblée nationale du Sénégal" },
        { property: "og:type", content: "website" },
        { property: "og:site_name", content: siteName },
        { name: "robots", content: "index, follow" },
      ],
    });
  }
});

// Chargement des données au montage
onMounted(async () => {
  await fetchAssemblyGroupById(groupId);
  await fetchElectedDeputies(groupId);
});

// Fonction de formatage de date
const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString("fr-FR", {
    day: "numeric",
    month: "long",
    year: "numeric",
  });
};
</script>
