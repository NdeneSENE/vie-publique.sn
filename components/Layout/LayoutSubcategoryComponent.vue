<template>
  <div class="py-6 sm:py-8">
    <!-- Header de section moderne -->
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <div class="text-center">
        <h2 class="text-xl font-bold tracking-tight text-gray-900 sm:text-2xl dark:text-white">
          {{ title }}
        </h2>
      </div>

      <!-- Grille des cartes avec animation séquentielle -->
      <div class="mt-6 sm:mt-8">
        <div class="grid grid-cols-1 gap-3 sm:grid-cols-2 lg:grid-cols-3 xl:gap-4">
          <NuxtLink
            v-for="(menu, index) in item"
            :key="menu.title"
            :to="menu.display ? menu.to : '#'"
            class="group relative block"
            :style="{ animationDelay: `${index * 80}ms` }"
            :class="{ 'pointer-events-none': !menu.display }"
          >
            <!-- Carte principale -->
            <div
              class="card-modern relative overflow-hidden rounded-xl border border-gray-200/60 bg-gradient-to-br transition-all duration-300 ease-out dark:border-gray-700/60"
              :class="[
                menu.display 
                  ? 'from-white to-gray-50 dark:from-gray-800 dark:to-gray-900 hover:shadow-lg hover:border-gray-300 dark:hover:border-gray-600' 
                  : 'from-gray-50 to-gray-100 dark:from-gray-900 dark:to-gray-950 opacity-60',
                menu.display ? 'group-hover:-translate-y-1' : ''
              ]"
            >
              <!-- Effet de brillance au hover (seulement si display = true) -->
              <div 
                v-if="menu.display"
                class="absolute inset-0 bg-gradient-to-r from-transparent via-white/10 to-transparent opacity-0 transition-opacity duration-500 group-hover:opacity-100 dark:via-white/5" 
              />

              <!-- Contenu de la carte -->
              <div class="relative p-4">
                <div class="flex items-start space-x-3">
                  <!-- Icône avec effet de background -->
                  <div 
                    class="flex h-10 w-10 flex-shrink-0 items-center justify-center rounded-xl transition-all duration-300"
                    :class="[
                      menu.display 
                        ? `${menu.color} ${menu.display ? 'group-hover:scale-110' : ''}` 
                        : 'bg-gray-200 text-gray-500 dark:bg-gray-700 dark:text-gray-400'
                    ]"
                  >
                    <UIcon
                      :name="menu.icon"
                      class="h-5 w-5 transition-all duration-300"
                    />
                  </div>

                  <!-- Texte -->
                  <div class="min-w-0 flex-1">
                    <h3 
                      class="text-sm font-semibold transition-colors duration-200"
                      :class="[
                        menu.display 
                          ? 'text-gray-900 group-hover:text-blue-600 dark:text-white dark:group-hover:text-blue-400' 
                          : 'text-gray-500 dark:text-gray-400'
                      ]"
                    >
                      {{ menu.title }}
                    </h3>
                    <p 
                      v-if="menu.description"
                      class="mt-1 text-xs transition-colors duration-200"
                      :class="[
                        menu.display 
                          ? 'text-gray-600 group-hover:text-gray-500 dark:text-gray-400 dark:group-hover:text-gray-300' 
                          : 'text-gray-400 dark:text-gray-500'
                      ]"
                    >
                      {{ menu.description }}
                    </p>
                  </div>

                  <!-- Flèche indicatrice (seulement si display = true) -->
                  <div v-if="menu.display" class="flex items-center">
                    <div class="flex h-6 w-6 items-center justify-center rounded-full bg-gray-50 transition-all duration-300 group-hover:bg-blue-50 group-hover:scale-110 dark:bg-gray-700 dark:group-hover:bg-blue-900/30">
                      <UIcon
                        name="i-heroicons-arrow-right"
                        class="h-3 w-3 text-gray-400 transition-all duration-300 group-hover:translate-x-0.5 group-hover:text-blue-600 dark:group-hover:text-blue-400"
                      />
                    </div>
                  </div>

                  <!-- Icône de verrouillage pour les éléments désactivés -->
                  <div v-else class="flex items-center">
                    <div class="flex h-6 w-6 items-center justify-center">
                      <UIcon
                        name="i-heroicons-lock-closed"
                        class="h-3 w-3 text-gray-400"
                      />
                    </div>
                  </div>
                </div>
              </div>

              <!-- Effet de border animé -->
              <div 
                class="absolute inset-0 rounded-xl ring-1 ring-inset transition-all duration-300"
                :class="[
                  menu.display 
                    ? 'ring-gray-900/5 group-hover:ring-blue-500/20 dark:ring-white/10 dark:group-hover:ring-blue-400/20' 
                    : 'ring-gray-900/5 dark:ring-white/5'
                ]"
              />
            </div>

            <!-- Shadow dynamique (seulement si display = true) -->
            <div 
              v-if="menu.display"
              class="absolute inset-0 -z-10 rounded-xl bg-gray-900/5 opacity-0 transition-all duration-300 group-hover:opacity-100 dark:bg-white/5" 
              style="transform: translate(2px, 2px)" 
            />
          </NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const props = defineProps<{
  item: any[];
  title: string;
}>();
</script>

<style scoped>
/* Animation d'apparition séquentielle */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(15px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.card-modern {
  animation: fadeInUp 0.5s ease-out forwards;
  opacity: 0;
}

/* États focus pour l'accessibilité */
.group:focus-visible .card-modern {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
}

/* Optimisations performance */
.card-modern {
  will-change: transform, box-shadow;
}

.group:hover .card-modern {
  will-change: auto;
}

/* Responsive améliorations */
@media (max-width: 640px) {
  .card-modern {
    border-radius: 0.75rem;
  }
}

/* Réduction d'animation pour l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .card-modern {
    animation: none;
    opacity: 1;
  }
  
  .group:hover .card-modern {
    transform: none;
  }
  
  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
@media print {
  .card-modern {
    break-inside: avoid;
    box-shadow: none !important;
    transform: none !important;
  }
}
</style>