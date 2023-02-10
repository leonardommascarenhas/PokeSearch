<template>
  <main>
    <form @submit.prevent="searchPokemon">
      <input type="text" v-model="pokemon.name" />
      <button type="submit">Submit</button>
    </form>

    <div v-if="pokemonArray != []">
      <Card
        v-for="(pokemon, index) in pokemonArray"
        :key="index"
        :name="pokemon.name"
        :stats="pokemon.stats"
        :imageSrc="pokemon.pokemonImage"
      />
    </div>
    <div v-if="pokemon.evolutionData">Evolutions:</div>
  </main>
</template>

<script>
import axios from "axios";
import Card from "./components/Card.vue";

export default {
  data() {
    return {
      pokemon: {
        name: "",
        stats: "",
        evolutionData: null,
        pokemonImage: "",
      },
      pokemonArray: [],
    };
  },
  components: { Card },
  methods: {
    async fetchPokemon(pokemon) {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${pokemon.name
            .replace("'", "")
            .replace(/[.: ']+/g, "-")
            .toLowerCase()}`
        );
        pokemon.stats = response.data.stats;
        pokemon.pokemonImage = response.data.sprites.front_default;
        const speciesUrl = response.data.species.url;

        const speciesResponse = await axios.get(speciesUrl);
        const evolutionUrl = speciesResponse.data.evolution_chain.url;

        const evolutionData = await axios.get(evolutionUrl);
        pokemon.evolutionData = evolutionData.data.chain.evolves_to[0];
        this.addPokemon(pokemon);
      } catch (error) {
        console.error(error);
        this.pokemon.evolutionData = null;
      }
    },
    async searchPokemon() {
      try {
        this.fetchPokemon(this.pokemon);
      } catch (error) {
        console.error(error);
        this.pokemon.evolutionData = null;
      }
    },
    async hasEvolution(pokemon) {
      try {
        if (pokemon.evolutionData.evolves_to !== []) {
          console.group(pokemon);
        }
      } catch {}
    },
    addPokemon(pokemon) {
      this.hasEvolution(this.pokemon);
      this.pokemonArray.push(pokemon);
      this.pokemon = {};
    },
  },
};
</script>