<template>
  <main>
    <form @submit.prevent="fetchPokemon">
      <input type="text" v-model="pokemonName" />
      <button type="submit">Submit</button>
    </form>

    <div v-if="stats">
      <Card :name="pokemonName" :stats="this.stats" :imageSrc="pokemonImage" />
    </div>
    <div v-if="evolutionChain">Evolutions:</div>
  </main>
</template>

<script>
import axios from "axios";
import Card from "./components/Card.vue";

export default {
  data() {
    return {
      pokemonName: "",
      stats: "",
      evolutionChain: null,
      pokemonImage: "",
    };
  },
  components: { Card },
  methods: {
    async fetchPokemon() {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${this.pokemonName.toLowerCase()}`
        );
        this.stats = response.data.stats;
        this.pokemonImage = response.data.sprites.front_default;
        const speciesUrl = response.data.species.url;

        const speciesResponse = await axios.get(speciesUrl);
        const evolutionUrl = speciesResponse.data.evolution_chain.url;

        const evolutionChain = await axios.get(evolutionUrl);
        this.evolutionChain = evolutionChain.data.chain;

        console.log(this.evolutionChain);
      } catch (error) {
        console.error(error);
        this.evolutionChain = null;
      }
    },
    async fetchEvolution() {
      try {
      } catch (error) {
        console.error(error);
        this.evolutionChain = null;
      }
    },
  },
};
</script>