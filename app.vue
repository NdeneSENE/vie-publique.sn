<script setup lang="ts">
import { Toaster, toast } from "vue-sonner";

const isOpen = ref(false);
const scrolled = ref(false);

// Appliquer le middleware globalement
definePageMeta({
  middleware: ["maintenance"],
});

const isChatPage = ref(useRoute().path === "/chatbot");
watch(
  () => useRoute().path,
  (newPath) => {
    isChatPage.value = newPath === "/chatbot";
  },
);

// Detection du scroll pour l'effet adaptatif
onMounted(() => {
  const handleScroll = () => {
    scrolled.value = window.scrollY > 10;
  };

  window.addEventListener("scroll", handleScroll);

  // Service Worker logic
  if (import.meta.client && "serviceWorker" in navigator) {
    navigator.serviceWorker.addEventListener("controllerchange", () => {});

    navigator.serviceWorker.ready.then((registration) => {
      if (registration.waiting) {
        toast.success("🚀 Nouvelle version disponible!", {
          description: "Cliquez pour mettre à jour l'application",
          action: {
            label: "Mettre à jour",
            onClick: () => location.reload(),
          },
          duration: 8000,
        });
      }

      registration.addEventListener("updatefound", () => {
        const newWorker = registration.installing;
        if (newWorker) {
          newWorker.addEventListener("statechange", () => {
            if (
              newWorker.state === "installed" &&
              navigator.serviceWorker.controller
            ) {
              toast.success("🚀 Nouvelle version disponible!", {
                description: "Cliquez pour mettre à jour l'application",
                action: {
                  label: "Mettre à jour",
                  onClick: () => location.reload(),
                },
                duration: 8000,
              });
            }
          });
        }
      });
    });
  }

  onBeforeUnmount(() => {
    window.removeEventListener("scroll", handleScroll);
  });
});

const links = [
  {
    label: "Accueil",
    icon: "i-heroicons-home",
    to: "/",
  },
  {
    label: "Actualités",
    description: "Communiqués, Annonces, Articles",
    icon: "i-heroicons-newspaper",
    to: "/actualites",
    badge: "Nouveau",
  },
  {
    label: "Assemblée",
    description: "Suivez l'activité parlementaire",
    icon: "i-heroicons-building-library",
    to: "/assemblee-nationale",
  },
  {
    label: "Annuaires",
    description: "Gouvernement, Sites Web, Justice...",
    icon: "i-heroicons-identification",
    to: "/annuaires",
  },
  {
    label: "Documents",
    description: "Journal officiel, Codes, Rapports...",
    icon: "i-heroicons-rectangle-stack",
    to: "/documents",
  },
  {
    label: "Budget",
    description: "Finances publiques",
    icon: "i-heroicons-banknotes",
    to: "/budget-senegal",
  },
  {
    label: "Élections",
    description: "Élections législatives du 17 Novembre",
    icon: "i-heroicons-information-circle",
    to: "/elections",
    badge: "Important",
  },
];

const aboutUslinks = [
  {
    label: "Conseil des ministres",
    description: "Décisions gouvernementales",
    icon: "i-heroicons-document-text",
    to: "/conseil-des-ministres",
  },
  {
    label: "Assemblée Nationale",
    description: "Institution parlementaire",
    to: "/assemblee-nationale",
    icon: "i-heroicons-building-library",
  },
  {
    label: "Newsletter",
    description: "Restez informés",
    icon: "i-heroicons-envelope",
    to: "/newsletter",
  },
  {
    label: "À Propos",
    description: "Notre mission",
    to: "/a-propos/qui-sommes-nous",
    icon: "i-heroicons-information-circle",
  },
];
</script>

<template>
  <div
    class="min-h-screen bg-white transition-colors duration-300 dark:bg-gray-900"
  >
    <!-- Header principal - Style Tailwind UI -->
    <header
      class="header-main fixed inset-x-0 top-0 z-50 transition-all duration-300"
      :class="scrolled ? 'header-scrolled shadow-lg' : 'header-initial'"
    >
      <!-- Gradient overlay pour le header -->
      <div
        class="absolute inset-0 bg-gradient-to-r from-blue-600 via-purple-600 to-blue-700 transition-all duration-300 dark:from-gray-900 dark:via-gray-800 dark:to-gray-900"
      />

      <!-- Glassmorphism overlay -->
      <div
        class="absolute inset-0 bg-white/10 backdrop-blur-xl dark:bg-black/20"
        :class="scrolled ? 'opacity-95' : 'opacity-90'"
      />

      <div class="relative">
        <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
          <div class="flex h-16 items-center justify-between">
            <!-- Logo et branding -->
            <div class="flex items-center">
              <div class="flex-shrink-0">
                <AppHeader />
              </div>
            </div>

            <!-- Navigation et actions desktop -->
            <div class="hidden lg:flex lg:items-center lg:gap-x-6">
              <AppSearch />
              <div class="h-6 w-px bg-white/20 dark:bg-gray-600" />
              <ThemeToggle />
            </div>

            <!-- Menu mobile -->
            <div class="flex items-center gap-x-3 lg:hidden">
              <AppSearch />
              <UButton
                icon="i-heroicons-bars-3"
                variant="ghost"
                size="sm"
                class="text-white transition-colors duration-200 hover:bg-white/10"
                @click="isOpen = true"
              />
            </div>
          </div>
        </div>
      </div>
    </header>

    <!-- Navigation secondaire - Style Tailwind UI -->
    <nav
      class="nav-secondary sticky top-16 z-40 hidden border-b border-gray-200 lg:block dark:border-gray-700"
    >
      <div class="bg-white/95 backdrop-blur-sm dark:bg-gray-900/95">
        <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
          <div class="flex justify-center">
            <div class="flex space-x-8">
              <NuxtLink
                v-for="link in links"
                :key="link.to"
                :to="link.to"
                class="nav-link-secondary group inline-flex items-center border-b-2 border-transparent px-1 py-4 text-sm font-medium transition-all duration-200"
                :class="
                  $route.path === link.to
                    ? 'nav-link-active'
                    : 'nav-link-inactive'
                "
              >
                <UIcon
                  :name="link.icon"
                  class="mr-2 h-4 w-4 transition-colors duration-200"
                />
                {{ link.label }}

                <!-- Badge -->
                <span
                  v-if="link.badge"
                  class="ml-2 inline-flex items-center rounded-full px-2 py-0.5 text-xs font-medium transition-all duration-200"
                  :class="
                    link.badge === 'Important'
                      ? 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-400'
                      : 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-400'
                  "
                >
                  {{ link.badge }}
                </span>
              </NuxtLink>
            </div>
          </div>
        </div>
      </div>
    </nav>

    <!-- Sidebar mobile - Style Tailwind UI -->
    <USlideover v-model="isOpen" side="left" :ui="{ width: 'w-80' }">
      <div
        class="flex h-full flex-col divide-y divide-gray-200 bg-white dark:divide-gray-700 dark:bg-gray-900"
      >
        <!-- Header du sidebar -->
        <div class="flex min-h-0 flex-1 flex-col">
          <div
            class="flex h-16 flex-shrink-0 items-center justify-between bg-gradient-to-r from-blue-600 to-purple-600 px-6"
          >
            <div class="flex items-center space-x-3">
              <div
                class="flex h-8 w-8 items-center justify-center rounded-lg bg-white/20 backdrop-blur-sm"
              >
                <span class="text-sm font-bold text-white">VP</span>
              </div>
              <div>
                <h2 class="text-base font-semibold text-white">
                  Vie-Publique.sn
                </h2>
                <p class="text-xs text-blue-100">République du Sénégal</p>
              </div>
            </div>
            <UButton
              icon="i-heroicons-x-mark"
              variant="ghost"
              size="sm"
              class="text-white hover:bg-white/10"
              @click="isOpen = false"
            />
          </div>

          <!-- Navigation mobile -->
          <nav class="flex-1 overflow-y-auto bg-white dark:bg-gray-900">
            <div class="space-y-6 px-3 py-6">
              <!-- Menu principal -->
              <div>
                <h3
                  class="px-3 text-xs font-semibold uppercase tracking-wide text-gray-500 dark:text-gray-400"
                >
                  Navigation
                </h3>
                <div class="mt-2 space-y-1">
                  <NuxtLink
                    v-for="link in links"
                    :key="link.to"
                    :to="link.to"
                    class="group flex items-center rounded-md px-3 py-2 text-sm font-medium transition-all duration-200"
                    :class="
                      $route.path === link.to
                        ? 'border-r-2 border-blue-600 bg-blue-50 text-blue-700 dark:border-blue-400 dark:bg-blue-900/50 dark:text-blue-300'
                        : 'text-gray-700 hover:bg-gray-50 hover:text-gray-900 dark:text-gray-300 dark:hover:bg-gray-800 dark:hover:text-white'
                    "
                    @click="isOpen = false"
                  >
                    <UIcon
                      :name="link.icon"
                      class="mr-3 h-5 w-5 flex-shrink-0 transition-colors duration-200"
                      :class="
                        $route.path === link.to
                          ? 'text-blue-600 dark:text-blue-400'
                          : 'text-gray-400 group-hover:text-gray-500 dark:group-hover:text-gray-300'
                      "
                    />
                    <span class="flex-1">{{ link.label }}</span>
                    <span
                      v-if="link.badge"
                      class="ml-auto inline-block rounded-full px-2 py-0.5 text-xs font-medium transition-all duration-200"
                      :class="
                        link.badge === 'Important'
                          ? 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-400'
                          : 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-400'
                      "
                    >
                      {{ link.badge }}
                    </span>
                  </NuxtLink>
                </div>
              </div>

              <!-- Menu secondaire -->
              <div>
                <h3
                  class="px-3 text-xs font-semibold uppercase tracking-wide text-gray-500 dark:text-gray-400"
                >
                  Services
                </h3>
                <div class="mt-2 space-y-1">
                  <NuxtLink
                    v-for="link in aboutUslinks"
                    :key="link.to"
                    :to="link.to"
                    class="group flex items-center rounded-md px-3 py-2 text-sm font-medium transition-all duration-200"
                    :class="
                      $route.path === link.to
                        ? 'border-r-2 border-blue-600 bg-blue-50 text-blue-700 dark:border-blue-400 dark:bg-blue-900/50 dark:text-blue-300'
                        : 'text-gray-700 hover:bg-gray-50 hover:text-gray-900 dark:text-gray-300 dark:hover:bg-gray-800 dark:hover:text-white'
                    "
                    @click="isOpen = false"
                  >
                    <UIcon
                      :name="link.icon"
                      class="mr-3 h-5 w-5 flex-shrink-0 transition-colors duration-200"
                      :class="
                        $route.path === link.to
                          ? 'text-blue-600 dark:text-blue-400'
                          : 'text-gray-400 group-hover:text-gray-500 dark:group-hover:text-gray-300'
                      "
                    />
                    <span class="flex-1">{{ link.label }}</span>
                  </NuxtLink>
                </div>
              </div>
            </div>
          </nav>

          <!-- Footer du sidebar -->
          <div
            class="flex flex-shrink-0 border-t border-gray-200 p-4 dark:border-gray-700"
          >
            <div class="flex w-full items-center justify-between">
              <div class="text-sm text-gray-500 dark:text-gray-400">
                Gouvernement du Sénégal
              </div>
              <ThemeToggle />
            </div>
          </div>
        </div>
      </div>
    </USlideover>

    <!-- Contenu principal -->
    <main class="pt-16 lg:pt-32">
      <UContainer class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
        <NuxtLayout>
          <NuxtPage />
        </NuxtLayout>
        <AppFooter />
      </UContainer>
    </main>

    <!-- Navigation bottom -->
    <AppBottomNav v-show="!isChatPage" />

    <!-- Toast notifications -->
    <Toaster position="top-right" :theme="'system'" />

    <!-- Composants utilitaires -->
    <NuxtPwaManifest />
    <NuxtLoadingIndicator
      color="linear-gradient(to right, #3b82f6, #8b5cf6)"
      height="3"
    />
    <ClientOnly>
      <AppLineAlert />
    </ClientOnly>
  </div>
</template>

<style scoped>
/* Header moderne avec transitions fluides */
.header-main {
  border-bottom: 1px solid transparent;
}

.header-initial {
  @apply border-white/10 dark:border-gray-700/50;
}

.header-scrolled {
  @apply border-gray-200/50 dark:border-gray-700;
}

/* Navigation secondaire - Style Tailwind UI */
.nav-link-secondary {
  position: relative;
}

.nav-link-active {
  @apply border-blue-500 text-blue-600 dark:border-blue-400 dark:text-blue-400;
}

.nav-link-inactive {
  @apply text-gray-500 hover:border-gray-300 hover:text-gray-700 dark:text-gray-400 dark:hover:border-gray-600 dark:hover:text-gray-300;
}

/* Effet hover sophistiqué pour la navigation */
.nav-link-secondary::before {
  content: "";
  position: absolute;
  bottom: -2px;
  left: 0;
  right: 0;
  height: 2px;
  background: linear-gradient(90deg, #3b82f6, #8b5cf6);
  transform: scaleX(0);
  transition: transform 0.3s ease;
  border-radius: 1px;
}

.nav-link-secondary:hover::before {
  transform: scaleX(1);
}

.nav-link-active::before {
  transform: scaleX(1);
}

/* Animations fluides */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.nav-secondary {
  animation: fadeIn 0.4s ease-out;
}

/* Custom scrollbar pour le sidebar */
.overflow-y-auto {
  scrollbar-width: thin;
  scrollbar-color: rgb(156 163 175) transparent;
}

.overflow-y-auto::-webkit-scrollbar {
  width: 6px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: transparent;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  @apply rounded-full bg-gray-300 dark:bg-gray-600;
}

.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  @apply bg-gray-400 dark:bg-gray-500;
}

/* Améliorations pour l'accessibilité */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}

/* Focus states améliorés */
.nav-link-secondary:focus,
.group:focus {
  @apply outline-none ring-2 ring-blue-500 ring-offset-2 dark:ring-offset-gray-900;
}

/* Responsive improvements */
@media (max-width: 1024px) {
  .pt-16 {
    padding-top: 4rem;
  }
}

/* Print styles */
@media print {
  .header-main,
  .nav-secondary {
    display: none !important;
  }

  main {
    padding-top: 0 !important;
  }

  .bg-gradient-to-r {
    background: #000 !important;
    color: #fff !important;
  }
}

/* Dark mode transitions */
.dark {
  color-scheme: dark;
}

/* Enhanced glassmorphism effect */
.backdrop-blur-xl {
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
}

/* Badge animations */
.badge-animate {
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.8;
  }
}

/* Smooth state transitions */
* {
  transition-property: color, background-color, border-color,
    text-decoration-color, fill, stroke, opacity, box-shadow, transform, filter,
    backdrop-filter;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 150ms;
}
</style>
