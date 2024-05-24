<script setup>
  import Card from './Card.vue';

  defineProps({
    characters: {
      type: Array,
      required: true,
    },
  });
</script>

<template>
  <div v-auto-animate class="card-list mt-8">
    <Card
      v-for="character in characters"
      :key="character.id"
      :name="character.name"
      :status="character.status"
      :species="character.species"
      :location="character.location"
      :firstEpisode="character.firstEpisode"
      :imageUrl="character.image"
    />
  </div>
</template>

<style>
  .card-list {
    width: 100%;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(600px, 1fr));
    gap: 30px;
  }

  @media (max-width: 640px) {
    .card-list {
      grid-template-columns: repeat(auto-fit, minmax(290px, 1fr));
    }
  }
</style>
