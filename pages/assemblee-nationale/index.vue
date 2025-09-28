<script setup lang="ts">
import { useNews } from "~/composables/news/useNews";
import { useAssemblyQuestions } from "~/composables/useAssemblyQuestions";
import { useAssemblyVotes } from "~/composables/useAssemblyVotes";

const { news } = useNews({ category: "assemblee-nationale" });
const { questions } = useAssemblyQuestions();
const { votes } = useAssemblyVotes();

// SEO (gardé identique)
const seoTitle = "Assemblée nationale du Sénégal - Activité parlementaire | Vie-Publique.sn";
const seoDescription = "Suivez l'activité parlementaire de l'Assemblée nationale du Sénégal : séances plénières, commissions, textes adoptés, questions au gouvernement et travaux parlementaires.";
const seoKeywords = "Assemblée nationale Sénégal, députés sénégalais, parlement sénégal, lois sénégal, séances plénières, commissions parlementaires";
const seoImgPath = "https://vie-publique.sn/seo-img-assemblee.png";
const seoPageUrl = "https://vie-publique.sn/assemblee-nationale";

useHead({
  title: seoTitle,
  meta: [
    { name: "description", content: seoDescription },
    { name: "keywords", content: seoKeywords },
    { name: "twitter:title", content: seoTitle },
    { name: "twitter:description", content: seoDescription },
    { name: "twitter:card", content: "summary_large_image" },
    { name: "twitter:image", content: seoImgPath },
    { property: "og:title", content: seoTitle },
    { property: "og:description", content: seoDescription },
    { property: "og:image", content: seoImgPath },
    { property: "og:url", content: seoPageUrl },
    { property: "og:type", content: "website" },
  ],
});
</script>

<template>
  <div class="py-6">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <!-- Header simple -->
      <div class="mb-8">
        <h1 class="text-xl font-semibold text-gray-900 dark:text-white">
          Assemblée nationale
        </h1>
        <p class="text-sm text-gray-600 dark:text-gray-400">
          15ème législature 2024-2029
        </p>
      </div>

      <!-- Navigation Cards - Layout simple et équilibré -->
      <div class="mb-12">
        <div class="grid grid-cols-2 gap-4 sm:grid-cols-3 lg:grid-cols-6">
          <!-- Députés -->
          <NuxtLink
            to="/assemblee-nationale/deputes"
            class="group relative lg:col-span-2"
          >
            <div class="h-full rounded-xl bg-white p-6 shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700">
              <div class="flex items-center justify-between">
                <div class="flex h-12 w-12 items-center justify-center rounded-xl bg-blue-50 dark:bg-blue-900/30">
                  <UIcon name="i-heroicons-users" class="h-6 w-6 text-blue-600 dark:text-blue-400" />
                </div>
                <div class="text-right">
                  <div class="text-2xl font-bold text-gray-900 dark:text-white">165</div>
                  <div class="text-xs text-gray-500 dark:text-gray-400">députés</div>
                </div>
              </div>
              <div class="mt-4">
                <h3 class="text-base font-semibold text-gray-900 dark:text-white">Députés</h3>
                <p class="text-sm text-gray-600 dark:text-gray-400">Élus de la nation</p>
              </div>
            </div>
          </NuxtLink>

          <!-- Questions -->
          <NuxtLink
            to="/assemblee-nationale/questions"
            class="group relative"
          >
            <div class="h-full rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700">
              <div class="mb-3 flex h-10 w-10 items-center justify-center rounded-lg bg-red-50 dark:bg-red-900/30">
                <UIcon name="i-heroicons-question-mark-circle" class="h-5 w-5 text-red-600 dark:text-red-400" />
              </div>
              <h3 class="text-sm font-medium text-gray-900 dark:text-white">Questions</h3>
              <div class="mt-2 text-xl font-bold text-gray-900 dark:text-white">234</div>
              <p class="text-xs text-gray-600 dark:text-gray-400">en cours</p>
            </div>
          </NuxtLink>

          <!-- Votes -->
          <NuxtLink
            to="/assemblee-nationale/votes"
            class="group relative"
          >
            <div class="h-full rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700">
              <div class="mb-3 flex h-10 w-10 items-center justify-center rounded-lg bg-green-50 dark:bg-green-900/30">
                <UIcon name="i-heroicons-document-check" class="h-5 w-5 text-green-600 dark:text-green-400" />
              </div>
              <h3 class="text-sm font-medium text-gray-900 dark:text-white">Votes</h3>
              <div class="mt-2 text-xl font-bold text-gray-900 dark:text-white">87</div>
              <p class="text-xs text-gray-600 dark:text-gray-400">adoptés</p>
            </div>
          </NuxtLink>

          <!-- Groupes -->
          <NuxtLink
            to="/assemblee-nationale/groupes"
            class="group relative"
          >
            <div class="h-full rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700">
              <div class="mb-3 flex h-10 w-10 items-center justify-center rounded-lg bg-emerald-50 dark:bg-emerald-900/30">
                <UIcon name="i-heroicons-user-group" class="h-5 w-5 text-emerald-600 dark:text-emerald-400" />
              </div>
              <h3 class="text-sm font-medium text-gray-900 dark:text-white">Groupes</h3>
              <div class="mt-2 text-xl font-bold text-gray-900 dark:text-white">8</div>
              <p class="text-xs text-gray-600 dark:text-gray-400">formations</p>
            </div>
          </NuxtLink>

          <!-- Commissions -->
          <NuxtLink
            to="/assemblee-nationale/commissions"
            class="group relative"
          >
            <div class="h-full rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700">
              <div class="mb-3 flex h-10 w-10 items-center justify-center rounded-lg bg-purple-50 dark:bg-purple-900/30">
                <UIcon name="i-heroicons-clipboard-document-list" class="h-5 w-5 text-purple-600 dark:text-purple-400" />
              </div>
              <h3 class="text-sm font-medium text-gray-900 dark:text-white">Commissions</h3>
              <div class="mt-2 text-xl font-bold text-gray-900 dark:text-white">12</div>
              <p class="text-xs text-gray-600 dark:text-gray-400">permanentes</p>
            </div>
          </NuxtLink>

          <!-- Bureau -->
          <NuxtLink
            to="/assemblee-nationale/bureau"
            class="group relative"
          >
            <div class="h-full rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700">
              <div class="mb-3 flex h-10 w-10 items-center justify-center rounded-lg bg-amber-50 dark:bg-amber-900/30">
                <UIcon name="i-heroicons-building-office-2" class="h-5 w-5 text-amber-600 dark:text-amber-400" />
              </div>
              <h3 class="text-sm font-medium text-gray-900 dark:text-white">Bureau</h3>
              <div class="mt-2 text-xl font-bold text-gray-900 dark:text-white">25</div>
              <p class="text-xs text-gray-600 dark:text-gray-400">membres</p>
            </div>
          </NuxtLink>
        </div>
      </div>

      <!-- Sections de contenu classiques -->
      <div>
        <h2 class="mb-6 text-lg font-semibold text-gray-900 dark:text-white">
          Activité parlementaire
        </h2>

        <!-- Desktop: Grid 3 colonnes -->
        <div class="hidden gap-6 md:grid md:grid-cols-3">
          <!-- Actualités -->
          <div class="rounded-xl bg-white p-6 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="mb-4 flex items-center justify-between">
              <div class="flex items-center">
                <div class="mr-3 flex h-8 w-8 items-center justify-center rounded-lg bg-blue-50 dark:bg-blue-900/30">
                  <UIcon name="i-heroicons-newspaper" class="h-4 w-4 text-blue-600 dark:text-blue-400" />
                </div>
                <h3 class="text-sm font-medium text-gray-900 dark:text-white">Actualités</h3>
              </div>
              <NuxtLink
                to="/assemblee-nationale/actualites"
                class="text-xs text-blue-600 hover:text-blue-700 dark:text-blue-400"
              >
                Voir tout
              </NuxtLink>
            </div>
            <AssemblyHomeNews :news="news" />
          </div>

          <!-- Questions -->
          <div class="rounded-xl bg-white p-6 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="mb-4 flex items-center justify-between">
              <div class="flex items-center">
                <div class="mr-3 flex h-8 w-8 items-center justify-center rounded-lg bg-red-50 dark:bg-red-900/30">
                  <UIcon name="i-heroicons-question-mark-circle" class="h-4 w-4 text-red-600 dark:text-red-400" />
                </div>
                <h3 class="text-sm font-medium text-gray-900 dark:text-white">Questions récentes</h3>
              </div>
              <NuxtLink
                to="/assemblee-nationale/questions"
                class="text-xs text-red-600 hover:text-red-700 dark:text-red-400"
              >
                Voir tout
              </NuxtLink>
            </div>
            <AssemblyHomeQuestions :questions="questions" />
          </div>

          <!-- Votes -->
          <div class="rounded-xl bg-white p-6 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="mb-4 flex items-center justify-between">
              <div class="flex items-center">
                <div class="mr-3 flex h-8 w-8 items-center justify-center rounded-lg bg-green-50 dark:bg-green-900/30">
                  <UIcon name="i-heroicons-document-check" class="h-4 w-4 text-green-600 dark:text-green-400" />
                </div>
                <h3 class="text-sm font-medium text-gray-900 dark:text-white">Votes récents</h3>
              </div>
              <NuxtLink
                to="/assemblee-nationale/votes"
                class="text-xs text-green-600 hover:text-green-700 dark:text-green-400"
              >
                Voir tout
              </NuxtLink>
            </div>
            <AssemblyHomeVotes :votes="votes" />
          </div>
        </div>

        <!-- Mobile: Stack vertical -->
        <div class="space-y-6 md:hidden">
          <!-- Questions -->
          <div class="rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="mb-4 flex items-center justify-between border-b border-gray-200 pb-4 dark:border-gray-700">
              <div class="flex items-center">
                <div class="mr-3 flex h-8 w-8 items-center justify-center rounded-lg bg-red-50 dark:bg-red-900/30">
                  <UIcon name="i-heroicons-question-mark-circle" class="h-4 w-4 text-red-600 dark:text-red-400" />
                </div>
                <h3 class="text-sm font-medium text-gray-900 dark:text-white">Questions récentes</h3>
              </div>
              <NuxtLink
                to="/assemblee-nationale/questions"
                class="text-xs text-red-600 hover:text-red-700 dark:text-red-400"
              >
                Voir tout
              </NuxtLink>
            </div>
            <AssemblyHomeQuestions :questions="questions" />
          </div>

          <!-- Actualités -->
          <div class="rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="mb-4 flex items-center justify-between border-b border-gray-200 pb-4 dark:border-gray-700">
              <div class="flex items-center">
                <div class="mr-3 flex h-8 w-8 items-center justify-center rounded-lg bg-blue-50 dark:bg-blue-900/30">
                  <UIcon name="i-heroicons-newspaper" class="h-4 w-4 text-blue-600 dark:text-blue-400" />
                </div>
                <h3 class="text-sm font-medium text-gray-900 dark:text-white">Actualités</h3>
              </div>
              <NuxtLink
                to="/assemblee-nationale/actualites"
                class="text-xs text-blue-600 hover:text-blue-700 dark:text-blue-400"
              >
                Voir tout
              </NuxtLink>
            </div>
            <AssemblyHomeNews :news="news" />
          </div>

          <!-- Votes -->
          <div class="rounded-xl bg-white p-4 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
            <div class="mb-4 flex items-center justify-between border-b border-gray-200 pb-4 dark:border-gray-700">
              <div class="flex items-center">
                <div class="mr-3 flex h-8 w-8 items-center justify-center rounded-lg bg-green-50 dark:bg-green-900/30">
                  <UIcon name="i-heroicons-document-check" class="h-4 w-4 text-green-600 dark:text-green-400" />
                </div>
                <h3 class="text-sm font-medium text-gray-900 dark:text-white">Votes récents</h3>
              </div>
              <NuxtLink
                to="/assemblee-nationale/votes"
                class="text-xs text-green-600 hover:text-green-700 dark:text-green-400"
              >
                Voir tout
              </NuxtLink>
            </div>
            <AssemblyHomeVotes :votes="votes" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Hover effects simples */
.group:hover {
  transform: translateY(-1px);
}

/* États focus pour l'accessibilité */
.group:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 0.75rem;
}

/* Performance */
.group {
  transition: transform 0.2s ease-out;
}

/* Print styles */
@media print {
  .group {
    transform: none !important;
  }
}
</style>