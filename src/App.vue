<script setup>
  import { onMounted, ref, watch } from 'vue';
  import axios from 'axios';

  import CardList from './components/CardList.vue';

  const characters = ref([]);
  const filters = ref({
    page: 1,
  });
  const pages = ref(0);

  const fetchEpisodes = async (ids = ['']) => {
    try {
      const { data } = await axios.get(
        `https://rickandmortyapi.com/api/episode/${ids.join()}`
      );

      return data;
    } catch (e) {
      console.log(e);
    }
  };

  const fetchCharacters = async (filters = {}) => {
    try {
      const { data } = await axios.get(
        'https://rickandmortyapi.com/api/character',
        {
          params: filters,
        }
      );

      pages.value = data.info.pages;
      return data.results;
    } catch (e) {
      console.log(e);
    }
  };

  const transformDataCharacters = async (chars) => {
    try {
      let newCharacters = [];
      let episodes = [];

      chars.forEach((item, i) => {
        episodes[i] = item.episode[0].substring(40);

        newCharacters[i] = {
          name: item.name,
          status: item.status,
          location: item.location.name,
          species: item.species,
          image: item.image,
          episodeId: +item.episode[0].substring(40),
        };
      });

      episodes = await fetchEpisodes(episodes);

      newCharacters = newCharacters.map((item) => {
        let findedEpisode = episodes.find(({ id }) => item.episodeId === id);
        return {
          ...item,
          firstEpisode: findedEpisode.name,
        };
      });

      return newCharacters;
    } catch (e) {
      console.log(e);
    }
  };

  const getCharacters = async () => {
    try {
      const chars = await fetchCharacters(filters.value);

      characters.value = await transformDataCharacters(chars);
    } catch (e) {
      console.log(e);
    }
  };

  const onChangeSearch = (e) => {
    filters.value.name = e.target.value;
  };

  const onChangeStatus = (e) => {
    e.target.value === 'Any'
      ? (filters.value.status = '')
      : (filters.value.status = e.target.value);
  };

  const onClickApply = async () => {
    filters.value.page = 1;
  };

  const onChangePage = async (e) => {
    filters.value.page = e.target.value;
  };

  const onClickGoPage = async (go) => {
    if (go === '+') {
      filters.value.page++;
    } else if (go === '-') {
      filters.value.page--;
    }
  };

  onMounted(async () => {
    await getCharacters();
  });

  watch(
    () => filters.value.page,
    async () => {
      await getCharacters();
    }
  );
</script>

<template>
  <div class="max-w-[1260px] px-3 mx-auto text-white">
    <div class="flex mt-8 flex-wrap gap-4">
      <input
        @input="onChangeSearch"
        class="p-2 bg-zinc-700 rounded mr-4 w-52"
        type="text"
        placeholder="Поиск ..."
      />
      <div>
        <span>Статус: </span>
        <select
          @change="onChangeStatus"
          class="p-2 bg-zinc-700 rounded mr-4 w-52"
        >
          <option value="Any">Любой</option>
          <option value="Alive">Жив</option>
          <option value="Dead">Мертв</option>
          <option value="Unknown">Неизвестно</option>
        </select>
      </div>
      <button
        @click="onClickApply"
        class="p-2 bg-neutral-500 rounded mr-4 w-52"
      >
        Применить
      </button>
    </div>
    <CardList :characters="characters" />
    <div class="flex my-8">
      <button
        @click="() => onClickGoPage('-')"
        v-if="filters.page > 1"
        class="p-2 bg-neutral-500 rounded mr-4 w-52"
      >
        <---
      </button>

      <select
        @change="onChangePage"
        :value="filters.page"
        class="p-2 bg-zinc-700 rounded mr-4"
      >
        <option v-for="page in pages" :value="page">{{ page }}</option>
      </select>

      <button
        @click="() => onClickGoPage('+')"
        v-if="filters.page < pages"
        class="p-2 bg-neutral-500 rounded mr-4 w-52"
      >
        --->
      </button>
    </div>
  </div>
</template>
