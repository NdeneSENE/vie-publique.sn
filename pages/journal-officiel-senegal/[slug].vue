<script setup lang="ts">

const route = useRoute();

const links = [
  { label: 'Journaux', to: '/journal-officiel-senegal' }]

</script>

<template>
  <ContentDoc v-slot="{ doc }">
    <AppBreadcrumb :links=links :lastText="route.params.slug" />

    <div class="prose prose-sm sm:prose mx-auto">
      <ContentRenderer :value="doc" />
    </div>
  </ContentDoc>

  <ScrollToTopButton />
</template>

<style>
.prose p a {
  /* text-decoration: none; */
  color: rgb(37 99 235);
}
</style>