<template>
  <div class="py-6 sm:py-8">
    <div class="mx-auto max-w-6xl px-4 sm:px-6 lg:px-8">
      <!-- Header épuré -->
      <div class="mb-8 text-center">
        <h2
          class="text-xl font-semibold text-gray-900 sm:text-2xl dark:text-white"
        >
          Nos réseaux sociaux
        </h2>
      </div>

      <!-- Grille compacte des réseaux sociaux -->
      <div class="grid grid-cols-2 gap-3 sm:grid-cols-4 sm:gap-4">
        <!-- LinkedIn Card -->
        <article
          class="social-card group relative"
          style="animation-delay: 0ms"
        >
          <a
            href="https://www.linkedin.com/company/vie-publique-sn"
            target="_blank"
            rel="noopener noreferrer"
            class="block h-full"
          >
            <div
              class="social-card-inner overflow-hidden rounded-xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600"
            >
              <div class="p-4 text-center">
                <!-- Container icône minimaliste -->
                <div
                  class="mx-auto mb-3 flex h-12 w-12 items-center justify-center rounded-xl bg-[#0A66C2]/10 transition-all duration-200 group-hover:scale-105 group-hover:bg-[#0A66C2]/20"
                >
                  <UIcon
                    name="i-simple-icons-linkedin"
                    class="h-5 w-5 text-[#0A66C2]"
                  />
                </div>

                <!-- Contenu compact -->
                <h3
                  class="mb-1 text-sm font-medium text-gray-900 dark:text-white"
                >
                  LinkedIn
                </h3>

                <p
                  class="text-lg font-semibold text-[#0A66C2] dark:text-[#60a5fa]"
                >
                  50K
                </p>
              </div>
            </div>
          </a>
        </article>

        <!-- Facebook Card -->
        <article
          class="social-card group relative"
          style="animation-delay: 100ms"
        >
          <a
            href="https://www.facebook.com/ViePubliqueSenegal"
            target="_blank"
            rel="noopener noreferrer"
            class="block h-full"
          >
            <div
              class="social-card-inner overflow-hidden rounded-xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600"
            >
              <div class="p-4 text-center">
                <!-- Container icône minimaliste -->
                <div
                  class="mx-auto mb-3 flex h-12 w-12 items-center justify-center rounded-xl bg-[#1877F2]/10 transition-all duration-200 group-hover:scale-105 group-hover:bg-[#1877F2]/20"
                >
                  <UIcon
                    name="i-simple-icons-facebook"
                    class="h-5 w-5 text-[#1877F2]"
                  />
                </div>

                <!-- Contenu compact -->
                <h3
                  class="mb-1 text-sm font-medium text-gray-900 dark:text-white"
                >
                  Facebook
                </h3>

                <p
                  class="text-lg font-semibold text-[#1877F2] dark:text-[#60a5fa]"
                >
                  20K
                </p>
              </div>
            </div>
          </a>
        </article>

        <!-- Instagram Card -->
        <article
          class="social-card group relative"
          style="animation-delay: 200ms"
        >
          <a
            href="https://www.instagram.com/viepubliquesn"
            target="_blank"
            rel="noopener noreferrer"
            class="block h-full"
          >
            <div
              class="social-card-inner overflow-hidden rounded-xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600"
            >
              <div class="p-4 text-center">
                <!-- Container icône minimaliste -->
                <div
                  class="mx-auto mb-3 flex h-12 w-12 items-center justify-center rounded-xl bg-gradient-to-br from-[#833AB4]/10 via-[#FD1D1D]/10 to-[#F77737]/10 transition-all duration-200 group-hover:scale-105"
                >
                  <UIcon
                    name="i-simple-icons-instagram"
                    class="h-5 w-5 bg-gradient-to-br from-[#833AB4] via-[#FD1D1D] to-[#F77737] bg-clip-text text-transparent"
                  />
                </div>

                <!-- Contenu compact -->
                <h3
                  class="mb-1 text-sm font-medium text-gray-900 dark:text-white"
                >
                  Instagram
                </h3>

                <p
                  class="text-lg font-semibold text-[#E4405F] dark:text-pink-400"
                >
                  2K
                </p>
              </div>
            </div>
          </a>
        </article>

        <!-- Twitter/X Card -->
        <article
          class="social-card group relative"
          style="animation-delay: 300ms"
        >
          <a
            href="https://x.com/ViePubliqueSN"
            target="_blank"
            rel="noopener noreferrer"
            class="block h-full"
          >
            <div
              class="social-card-inner overflow-hidden rounded-xl bg-white shadow-sm ring-1 ring-gray-200 transition-all duration-200 group-hover:shadow-md group-hover:ring-gray-300 dark:bg-gray-800 dark:ring-gray-700 dark:group-hover:ring-gray-600"
            >
              <div class="p-4 text-center">
                <!-- Container icône minimaliste -->
                <div
                  class="mx-auto mb-3 flex h-12 w-12 items-center justify-center rounded-xl bg-gray-100 transition-all duration-200 group-hover:scale-105 group-hover:bg-gray-200 dark:bg-gray-700 dark:group-hover:bg-gray-600"
                >
                  <UIcon
                    name="i-simple-icons-x"
                    class="h-5 w-5 text-black dark:text-white"
                  />
                </div>

                <!-- Contenu compact -->
                <h3
                  class="mb-1 text-sm font-medium text-gray-900 dark:text-white"
                >
                  Twitter
                </h3>

                <p class="text-lg font-semibold text-black dark:text-white">
                  16K
                </p>
              </div>
            </div>
          </a>
        </article>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
// Les statistiques sont mises à jour manuellement pour le moment
// TODO: Intégrer les API des réseaux sociaux pour des statistiques en temps réel
</script>

<style scoped>
/* Animation d'apparition simplifiée */
@keyframes socialCardFadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.social-card {
  animation: socialCardFadeIn 0.4s ease-out forwards;
  opacity: 0;
}

/* Effet de lift subtil */
.social-card-inner {
  transition:
    transform 0.2s ease-out,
    box-shadow 0.2s ease-out;
}

.group:hover .social-card-inner {
  transform: translateY(-2px);
}

/* États focus pour l'accessibilité */
.social-card a:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
  border-radius: 0.75rem;
}

/* Responsive pour mobile */
@media (max-width: 640px) {
  .social-card-inner .p-4 {
    padding: 0.875rem;
  }

  .h-12.w-12 {
    height: 2.75rem;
    width: 2.75rem;
  }

  .text-lg {
    font-size: 1rem;
  }

  .text-sm {
    font-size: 0.8rem;
  }
}

/* Amélioration de l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  .social-card {
    animation: none;
    opacity: 1;
  }

  .group:hover .social-card-inner {
    transform: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}

/* Performance optimizations */
.social-card-inner {
  will-change: transform;
}

.group:hover .social-card-inner {
  will-change: auto;
}

/* Instagram gradient text effect */
.bg-gradient-to-br.bg-clip-text {
  -webkit-background-clip: text;
  background-clip: text;
}

/* Hover subtil pour les conteneurs d'icônes */
.group:hover [class*="bg-[#0A66C2]"] {
  background-color: rgba(10, 102, 194, 0.2);
}

.group:hover [class*="bg-[#1877F2]"] {
  background-color: rgba(24, 119, 242, 0.2);
}

.dark .group:hover [class*="bg-gray-100"] {
  background-color: rgb(75 85 99);
}
</style>
