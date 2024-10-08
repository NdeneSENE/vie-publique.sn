<script setup lang="ts">
import type { MagistratureType } from "~/types/magistrature-type";

const seoTitle = 'Annuaire magistrature Sénégal';
const seoDescription = 'Liste des nominations du conseil supérieur de la magistrature avec le président Diomaye Faye au Sénégal';
const seoImgPath = 'https://vie-publique.sn/images/vpsn-share-magistrature.png';
const seoPageUrl = 'https://vie-publique.sn/justice/magistrature';
useHead({
    title: seoTitle,
    meta: [
        {
            name: 'description',
            content: seoDescription
        },
        // Twitter Card Meta Tags
        {
            name: "twitter:title",
            content: seoTitle,
        },
        {
            name: "twitter:description",
            content: seoDescription,
        },
        { name: "twitter:card", content: "summary_large_image" },
        { name: "twitter:image", content: seoImgPath },
        // Open Graph Meta Tags
        {
            property: "og:title",
            content: seoTitle,
        },
        {
            property: "og:description",
            content: seoDescription,
        },
        { property: "og:image", content: seoImgPath },
        { property: "og:url", content: seoPageUrl },
        { property: "og:type", content: "website" },
    ]
})

/* plugin */

const { $dateformat } = useNuxtApp()

/* Get Datas */

const nuxtApp = useNuxtApp()
const { data } = await useFetch('/api/nominations-magistrature', {
    watch: false,

    transform(input) {
        return {
            appointees: input,
            fetchedAt: new Date()
        }
    },

    getCachedData(key) {
        const data = nuxtApp.payload.data[key] || nuxtApp.static.data[key]
        // If data is not fetched yet
        if (!data) {
            // Fetch the first time
            return
        }

        // Is the data too old?
        const expirationDate = new Date(data.fetchedAt)
        expirationDate.setTime(expirationDate.getTime() + 120 * 1000) // 120 secondes
        const isExpired = expirationDate.getTime() < Date.now()
        if (isExpired) {
            // Refetch the data
            return
        }

        return data
    },
})

/* Filters */

const searchQuery = ref('');
const selectedJurisdictionType = ref('');
const selectedGender = ref('');

const filteredAppointee = computed(() => {
    return (data.value.appointees?.filter((appointee: MagistratureType) =>
        (appointee.name.toLowerCase().includes(searchQuery.value.toLowerCase())
            || appointee.new_position.toLowerCase().includes(searchQuery.value.toLowerCase())) &&
        (!selectedJurisdictionType.value || appointee.jurisdiction_attached === selectedJurisdictionType.value) &&
        (!selectedGender.value || appointee.gender === selectedGender.value)
    ) || []).sort((a, b) => new Date(b.appointment_date).getTime() - new Date(a.appointment_date).getTime());
});

/* Pagination */

const page = ref(1)
const pageCount = 25

const rowsFilteredAppointee = computed(() => {
    return filteredAppointee.value.slice((page.value - 1) * pageCount, (page.value) * pageCount)
});

// FIXME move to server
const totalsByJurisdictionType = computed(() => {
    const totals: Record<string, number> = {};
    data.value.appointees?.forEach((appointee: MagistratureType) => {
        if (appointee.jurisdiction_attached) {
            if (!totals[appointee.jurisdiction_attached]) {
                totals[appointee.jurisdiction_attached] = 0;
            }
            totals[appointee.jurisdiction_attached]++;
        }
    });

    // Convert object to array, sort alphabetically, then convert back to object
    return Object.fromEntries(
        Object.entries(totals).sort(([keyA], [keyB]) => keyA.localeCompare(keyB))
    );

    // return totals;
});

// FIXME move to server
const totalsByGender = computed(() => {
    let maleCount = 0;
    let femaleCount = 0;
    data.value.appointees?.forEach((appointee: MagistratureType) => {
        if (appointee.gender === 'Monsieur') {
            maleCount++;
        } else if (appointee.gender === 'Madame') {
            femaleCount++;
        }
    });
    return { maleCount, femaleCount };
});

const selectedAppointee = ref<MagistratureType | null>(null);
const isModalOpen = ref(false);

function openModal(appointee: MagistratureType) {
    selectedAppointee.value = appointee;
    isModalOpen.value = true;
}

// Réinitialiser la page lors d'une recherche
watch(searchQuery, () => {
    selectedJurisdictionType.value = '';
    page.value = 1;
});
// Réinitialiser la page lors du changement de type
watch(selectedJurisdictionType, () => {
    page.value = 1;
});

</script>

<template>
    <div class="flex flex-col items-center px-4">
        <div class="prose prose-sm sm:prose mx-auto my-2">
            <h1 class="text-center">
                Annuaire Magistrature Sénégal
            </h1>
        </div>

        <div class="text-center">
            <p v-if="filteredAppointee.length > 1" class="text-sm mb-2 text-gray-500">
                {{ filteredAppointee.length }} Magistrats référencés
            </p>
        </div>

        <!-- Modal pour afficher les détails du membre -->
        <UModal v-model="isModalOpen">
            <UCard v-if="selectedAppointee" :ui="{ ring: '', divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">
                <template #header>
                    <div class="flex justify-center items-center">
                        <NuxtImg :src="selectedAppointee.photo || '/unknown_member.webp'" alt="Profile Photo"
                            sizes="300px md:400px" :placeholder="[300, 300]" />
                    </div>
                </template>

                <div class="px-4 text-center">
                    <p class="text-sm text-gray-500">{{ selectedAppointee.gender }}</p>
                    <h2 class="text-2xl font-semibold">{{ selectedAppointee.name }}</h2>
                    <p class="text-md">{{ selectedAppointee.new_position }}</p>
                    <div v-if="selectedAppointee.appointment_date != null" class="mt-1">
                        <p class="text-sm text-gray-500">Nommé le</p>
                        <p class="text-sm">{{ $dateformat(selectedAppointee.appointment_date) }}</p>
                    </div>
                    <div v-if="selectedAppointee.previous_position != null" class="mt-1">
                        <p class="text-sm text-gray-500">précédemment</p>
                        <p class="text-sm">{{ selectedAppointee.previous_position }}</p>
                    </div>
                    <div v-if="selectedAppointee.predecessor != null" class="mt-1">
                        <p class="text-sm text-gray-500">Prédécesseur</p>
                        <p class="text-sm">{{ selectedAppointee.predecessor }}</p>
                    </div>

                </div>

                <template #footer>
                    <Placeholder class="h-8" />
                    <div class="text-right p-2">
                        <button class="btn btn-primary" @click="isModalOpen = false">Fermer</button>
                    </div>
                </template>
            </UCard>
        </UModal>

        <div class="w-full max-w-4xl">
            <UInput class="input w-full mb-3 custom-shadow" size="lg" icon="i-heroicons-magnifying-glass"
                placeholder="Rechercher une nomination..." v-model="searchQuery">
            </UInput>

            <div class="text-center w-full mb-1">
                <UButton :ui="{ rounded: 'rounded-full' }" class="text-sm custom-shadow ml-1 mb-1 font-normal"
                    :color="selectedGender === 'Monsieur' ? 'primary' : 'white'"
                    @click="selectedGender = selectedGender === 'Monsieur' ? '' : 'Monsieur'" size="sm">
                    Hommes
                    <UBadge :ui="{ rounded: 'rounded-full' }" :label="totalsByGender.maleCount"
                        :color="selectedGender === 'Monsieur' ? 'primary' : 'primary'"
                        :variant="selectedGender === 'Monsieur' ? 'soft' : 'solid'" size="xs"></UBadge>
                </UButton>
                <UButton :ui="{ rounded: 'rounded-full' }" class="text-sm custom-shadow ml-1 mb-1 font-normal"
                    :color="selectedGender === 'Madame' ? 'primary' : 'white'"
                    @click="selectedGender = selectedGender === 'Madame' ? '' : 'Madame'" size="sm">
                    Femmes
                    <UBadge :ui="{ rounded: 'rounded-full' }" :label="totalsByGender.femaleCount" color="primary"
                        :variant="selectedGender === 'Madame' ? 'soft' : 'solid'" size="xs"></UBadge>
                </UButton>
            </div>

            <div class="text-center w-full mb-1">
                <UButton v-for="(total, type) in totalsByJurisdictionType" :key="type" :ui="{ rounded: 'rounded-full' }"
                    :color="selectedJurisdictionType === type ? 'primary' : 'white'"
                    class="text-sm custom-shadow ml-1 mb-1 font-normal"
                    @click="selectedJurisdictionType = selectedJurisdictionType === type ? '' : type" size="sm">
                    {{ type }}
                    <UBadge :ui="{ rounded: 'rounded-full' }" :label="total" color="primary"
                        :variant="selectedJurisdictionType === type ? 'soft' : 'solid'" size="xs"></UBadge>
                </UButton>
            </div>

            <div class="text-center w-full mb-3">
                <NuxtLink to="/publications/justice/conseil-superieur-de-la-magistrature-18-aout-2024"
                    class="text-sm mb-2 underline text-center">
                    📄 Procès-verbal du CSM du 09 août 2024
                </NuxtLink>
            </div>

            <div class="space-y-2">
                <UCard v-for="appointee in rowsFilteredAppointee" :key="appointee.name"
                    class="cursor-pointer custom-shadow" @click="openModal(appointee)">
                    <div class="flex flex-row gap-2 ">
                        <div class="flex-shrink-0 w-16 h-16 md:w-20 md:h-20">
                            <NuxtImg :src="appointee.photo || '/unknown_member.webp'" alt="Photo ministre"
                                sizes="64px sm:80px" class="object-cover rounded-full w-full h-full" placeholder />


                        </div>
                        <div class="flex-grow">
                            <h2 class="font-semibold">{{ appointee.name }}</h2>
                            <p class="text-sm">{{ appointee.new_position }}</p>
                            <p class="text-sm text-gray-500">Nommé le
                                {{ $dateformat(appointee.appointment_date) }}
                            </p>
                            <p v-if="appointee.endDate != null" class="text-sm text-gray-500">Limogé le
                                {{ $dateformat(appointee.endDate) }}
                            </p>
                        </div>
                    </div>
                </UCard>
            </div>

            <div :class="{ 'hidden': rowsFilteredAppointee < pageCount }"
                class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
                <UPagination size="md" v-model="page" :page-count="pageCount" :total="filteredAppointee.length" />
            </div>
        </div>

    </div>
</template>