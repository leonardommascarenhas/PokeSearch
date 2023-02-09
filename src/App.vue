<template>
  <div>
    <form @submit.prevent="fetchPokemon">
      <input type="text" v-model="pokemonName" />
      <button type="submit">Submit</button>
    </form>

    <div v-if="stats">
      <img :src="pokemonImage" alt="" />
      <ul>
        <li v-for="stat in stats" :key="stat.stat.name">
          {{ stat.stat.name }}: {{ stat.base_stat }}
        </li>
      </ul>
    </div>
    <div v-if="evolutionChain">
      Evolutions:
      <ul></ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      pokemonName: "",
      stats: "",
      evolutionChain: null,
      pokemonImage: "",
    };
  },
  methods: {
    async fetchPokemon() {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${this.pokemonName}`
        );
        this.stats = response.data.stats;
        this.pokemonImage = response.data.sprites.front_default;
        const speciesUrl = response.data.species.url;
        const speciesResponse = await axios.get(speciesUrl);
        const evolutionUrl = speciesResponse.data.evolution_chain.url;
        const evolutionChain = await axios.get(evolutionUrl);
        this.evolutionChain = evolutionChain.data.chain;
      } catch (error) {
        console.error(error);
        this.evolutionChain = null;
      }
    },
  },
};
</script>