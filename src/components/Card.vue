<script setup>
  const props = defineProps({
    name: String,
    status: String,
    species: String,
    location: String,
    imageUrl: String,
    firstEpisode: String,
  });

  const statusList = {
    Alive: 'bg-emerald-400',
    Dead: 'bg-red-400',
    unknown: 'bg-neutral-400',
  };
</script>

<template>
  <div
    class="grid grid-cols-[230px_1fr] max-[640px]:grid-cols-1 bg-zinc-700 rounded-lg text-white overflow-hidden"
  >
    <div>
      <img class="w-full" :src="imageUrl" />
    </div>

    <div class="p-3.5">
      <h2 class="text-2xl font-bold">{{ name }}</h2>

      <span class="font-medium">
        <span
          :class="`${statusList[status]} inline-block w-2 h-2 rounded mr-1`"
        ></span>
        {{ status }} - {{ species }}
      </span>

      <div class="mt-3">
        <span class="text-neutral-400 font-medium">Last known location:</span>
        <p class="text-lg">{{ location }}</p>
      </div>

      <div class="mt-3">
        <span class="text-neutral-400 font-medium">First seen in:</span>
        <p class="text-lg">{{ firstEpisode }}</p>
      </div>
    </div>
  </div>
</template>
