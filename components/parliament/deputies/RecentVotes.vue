<script setup lang="ts">
import type { Vote } from "@/types/vote";

interface RecentVotesProps {
  votes: Vote[];
}

// Un computed label vote pour afficher le vote en toute lettre
const labelVote = (vote: number | null) => {
  if (vote === 0) return "Contre";
  if (vote === 1) return "Pour";
  return "Non voté";
};

defineProps<RecentVotesProps>();
</script>

<template>
  <div class="rounded-lg border bg-white p-6 shadow-sm">
    <h2 class="mb-4 text-2xl font-semibold">Ses derniers votes</h2>
    <div class="space-y-4">
      <div
        v-for="(vote, index) in votes"
        :key="index"
        class="flex items-center gap-4"
      >
        <div
          :class="{
            'font-semibold text-red-600': vote.vote === 0,
            'font-semibold text-green-600': vote.vote === 1,
            'text-gray-600': vote.vote === null,
          }"
        >
          {{ labelVote(vote.vote) }}
        </div>
        <div class="flex-1">
          <p>{{ vote.motion.title }}</p>
          <p class="text-sm text-gray-500">{{ vote.motion.description }}</p>
        </div>
      </div>
    </div>
  </div>
</template>
