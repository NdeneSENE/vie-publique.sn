<script setup lang="ts">
interface MotionVoteProps {
  title: string;
  description: string;
}

defineProps<MotionVoteProps>();
</script>

<template>
  <div class="rounded-lg border bg-white p-6 shadow-sm">
    <div class="flex items-start gap-3">
      <div class="rounded-lg bg-blue-100 p-2">📋</div>
      <div class="flex-1">
        <h2 class="mb-4 text-xl font-semibold">{{ title }}</h2>
        <p class="mb-2">
          {{ description }}
        </p>
      </div>
    </div>
  </div>
</template>
