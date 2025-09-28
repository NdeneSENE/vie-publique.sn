<template>
  <div class="py-6 sm:py-8">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <!-- Header -->
      <div class="text-center">
        <h2 class="text-xl font-bold tracking-tight text-gray-900 sm:text-2xl dark:text-white">
          Dernières initiatives parlementaires
        </h2>
        <p class="mt-2 text-base text-gray-600 dark:text-gray-300 sm:mt-3">
          Suivez l'activité de nos députés à l'Assemblée Nationale
        </p>
      </div>

      <!-- Content -->
      <div class="mt-6 sm:mt-8">
        <!-- Loading state -->
        <div v-if="loading" class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
          <div 
            v-for="n in 6" 
            :key="n" 
            class="parliament-skeleton animate-pulse"
            :style="{ animationDelay: `${n * 120}ms` }"
          >
            <div class="overflow-hidden rounded-xl bg-white shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700">
              <div class="p-4">
                <div class="flex items-start gap-3 mb-3">
                  <div class="h-10 w-10 rounded-full bg-gray-200 dark:bg-gray-700"></div>
                  <div class="flex-1 space-y-1">
                    <div class="h-3 w-20 rounded bg-gray-200 dark:bg-gray-600"></div>
                    <div class="h-2 w-16 rounded bg-gray-200 dark:bg-gray-600"></div>
                  </div>
                  <div class="h-5 w-12 rounded-full bg-gray-200 dark:bg-gray-600"></div>
                </div>
                <div class="space-y-2">
                  <div class="h-3 w-full rounded bg-gray-200 dark:bg-gray-600"></div>
                  <div class="h-3 w-4/5 rounded bg-gray-200 dark:bg-gray-600"></div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Error state -->
        <div v-else-if="error" class="text-center py-8">
          <div class="mx-auto flex h-12 w-12 items-center justify-center rounded-full bg-red-100 dark:bg-red-900/30">
            <UIcon name="i-heroicons-exclamation-triangle" class="h-6 w-6 text-red-600 dark:text-red-400" />
          </div>
          <h3 class="mt-3 text-base font-medium text-gray-900 dark:text-white">
            Erreur de chargement
          </h3>
          <p class="mt-1 text-sm text-gray-600 dark:text-gray-400">
            {{ error }}
          </p>
          <button
            @click="refreshQuestions()"
            class="mt-3 inline-flex items-center rounded-lg bg-red-600 px-3 py-2 text-sm font-medium text-white shadow-sm hover:bg-red-700"
          >
            Réessayer
          </button>
        </div>

        <!-- Empty state -->
        <div v-else-if="!questions || questions.length === 0" class="text-center py-8">
          <div class="mx-auto flex h-12 w-12 items-center justify-center rounded-full bg-gray-100 dark:bg-gray-800">
            <UIcon name="i-heroicons-building-library" class="h-6 w-6 text-gray-400" />
          </div>
          <h3 class="mt-3 text-base font-medium text-gray-900 dark:text-white">
            Aucune initiative disponible
          </h3>
          <p class="mt-1 text-sm text-gray-600 dark:text-gray-400">
            Aucune question parlementaire n'est disponible pour le moment
          </p>
        </div>

        <!-- Questions grid -->
        <div v-else>
          <div class="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
            <article
              v-for="(question, index) in questions?.slice(0, 6)"
              :key="question.id"
              class="parliament-card group relative"
              :style="{ animationDelay: `${index * 120}ms` }"
            >
              <NuxtLink
                :to="`/assemblee-nationale/questions/${question.id}`"
                class="block h-full"
              >
                <div class="parliament-card-inner overflow-hidden rounded-xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-300 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600">
                  <div class="p-4">
                    <!-- Header avec député et badge -->
                    <div class="flex items-start gap-3 mb-3">
                      <!-- Photo du député -->
                      <div class="relative flex-shrink-0">
                        <img
                          :src="$directusImageUrl(question.deputy.photo, '40')"
                          :alt="`${question.deputy.first_name} ${question.deputy.last_name}`"
                          class="h-10 w-10 rounded-full object-cover ring-2 ring-white shadow-sm transition-transform duration-300 group-hover:scale-105 dark:ring-gray-700"
                          loading="lazy"
                        />
                        <!-- Dot pulse repositionné -->
                        <div class="absolute -bottom-1 -right-1 h-3 w-3 rounded-full bg-green-400 ring-2 ring-white dark:ring-gray-800">
                          <div class="ping-animation absolute inset-0 rounded-full bg-green-400 opacity-75"></div>
                        </div>
                      </div>

                      <!-- Info député -->
                      <div class="flex-1 min-w-0 overflow-hidden">
                        <h4 class="text-sm font-semibold text-gray-900 dark:text-white truncate">
                          {{ question.deputy.first_name }} {{ question.deputy.last_name }}
                        </h4>
                        <p class="text-xs text-gray-500 dark:text-gray-400">
                          Député
                        </p>
                      </div>
                      
                      <!-- Badge -->
                      <div class="flex-shrink-0">
                        <span class="inline-flex items-center rounded-full bg-blue-100 px-2 py-0.5 text-xs font-medium text-blue-800 dark:bg-blue-900/30 dark:text-blue-400">
                          Q
                        </span>
                      </div>
                    </div>

                    <!-- Sujet -->
                    <div class="space-y-2">
                      <h3 class="line-clamp-2 text-sm font-medium leading-tight text-gray-900 transition-colors duration-200 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400">
                        {{ question.subject }}
                      </h3>
                      
                      <!-- Footer -->
                      <div class="flex items-center justify-between pt-1">
                        <div class="flex items-center text-xs text-gray-500 dark:text-gray-400">
                          <UIcon name="i-heroicons-calendar-days" class="mr-1 h-3 w-3" />
                          <span class="truncate">{{ $dateformat(question.question_date) }}</span>
                        </div>
                        
                        <!-- Flèche -->
                        <div class="flex h-6 w-6 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:bg-blue-50 group-hover:scale-110 dark:bg-gray-700 dark:group-hover:bg-blue-900/30 flex-shrink-0">
                          <UIcon 
                            name="i-heroicons-arrow-up-right" 
                            class="h-3 w-3 text-gray-400 transition-colors duration-300 group-hover:text-blue-600 dark:group-hover:text-blue-400" 
                          />
                        </div>
                      </div>
                    </div>
                  </div>

                  <!-- Effet border -->
                  <div class="absolute inset-0 rounded-xl ring-1 ring-inset ring-gray-900/5 transition-all duration-300 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20"></div>
                </div>
              </NuxtLink>
            </article>
          </div>

          <!-- Call to action -->
          <div class="mt-8 text-center">
            <NuxtLink
              to="/assemblee-nationale"
              class="group inline-flex items-center rounded-full bg-white px-5 py-2.5 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 transition-all duration-200 hover:bg-gray-50 hover:shadow-md hover:ring-gray-400 dark:bg-gray-800 dark:text-white dark:ring-gray-700 dark:hover:bg-gray-700 dark:hover:ring-gray-600"
            >
              Voir toute l'activité parlementaire
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

<script setup lang="ts">
import { useAssemblyQuestions } from "~/composables/useAssemblyQuestions";

const { questions, loading, error, refresh: refreshQuestions } = useAssemblyQuestions();
</script>

<style scoped>
/* Animation apparition */
@keyframes parliamentCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(15px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.parliament-card {
  animation: parliamentCardFadeIn 0.5s ease-out forwards;
  opacity: 0;
}

/* Hover lift */
.parliament-card-inner {
  transition: transform 0.3s ease-out, box-shadow 0.3s ease-out;
}
.group:hover .parliament-card-inner {
  transform: translateY(-2px);
}

/* Ping animation */
@keyframes ping {
  75%, 100% {
    transform: scale(2);
    opacity: 0;
  }
}
.ping-animation {
  animation: ping 2s cubic-bezier(0, 0, 0.2, 1) infinite;
}

/* Skeleton */
.parliament-skeleton {
  animation: parliamentCardFadeIn 0.5s ease-out forwards;
  opacity: 0;
}

/* Line clamp */
.line-clamp-2 {
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}
</style>
