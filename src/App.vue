<template>
  <main>
    <form @submit.prevent="fetchPokemon">
      <input type="text" v-model="pokemonName" />
      <button type="submit">Submit</button>
    </form>

    <div v-if="stats">
      <Card :name="pokemonName" :stats="this.stats" :imageSrc="pokemonImage" />
    </div>
    <div v-if="evolutionData">Evolutions:</div>
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
      evolutionData: null,
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

        const evolutionData = await axios.get(evolutionUrl);
        this.evolutionData = evolutionData.data.chain;
        this.fetchEvolution();
      } catch (error) {
        console.error(error);
        this.evolutionData = null;
      }
    },
    async fetchEvolution() {
      try {
        let antiBreak = 0;
        let hasNextEvolution = this.evolutionData.evolves_to.length;
        while (hasNextEvolution > 0) {
          let evolution = this.evolutionData.evolves_to[0];
          this.evolutionData = evolution;
          console.log(this.evolutionData);
          if (antiBreak > 20) {
            break;
          }
        }
      } catch (error) {
        console.error(error);
        this.evolutionData = null;
      }
    },
  },
};
</script>