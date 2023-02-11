<template>
  <main>
    <section>
      <h1>PokeSearch!</h1>
    </section>
    <section>
      <form @submit.prevent="searchPokemon">
        <input
          type="text"
          v-model="searchName"
          placeholder="What's your favorite pokemon?"
        />
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
    </section>
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
        pokemonImage: "",
      },
      evolutionData: null,
      searchName: "",
      pokemonArray: [],
    };
  },
  components: { Card },
  methods: {
    async fetchPokemon(pokemon) {
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${pokemon
            .replace("'", "")
            .replace(/[.: ']+/g, "-")
            .toLowerCase()}`
        );
        this.pokemon.name = response.data.species.name;
        this.pokemon.stats = response.data.stats;
        this.pokemon.pokemonImage = response.data.sprites.front_default;
        const speciesUrl = response.data.species.url;
        const speciesResponse = await axios.get(speciesUrl);
        const evolutionUrl = speciesResponse.data.evolution_chain.url;
        const evolutionData = await axios.get(evolutionUrl);

        this.evolutionData = evolutionData.data.chain;

        let currentPokemon = this.evolutionData;

        while (currentPokemon) {
          let response = await axios.get(
            `https://pokeapi.co/api/v2/pokemon/${currentPokemon.species.name.toLowerCase()}`
          );
          this.pokemon.name = response.data.name;
          this.pokemon.stats = response.data.stats;
          this.pokemon.pokemonImage = response.data.sprites.front_default;
          this.pokemonArray.push(this.pokemon);
          this.pokemon = {};

          currentPokemon = currentPokemon.evolves_to[0];
        }
      } catch (error) {
        console.error(error);
        this.evolutionData = null;
      }
    },
    async searchPokemon() {
      try {
        this.pokemonArray = [];
        this.fetchPokemon(this.searchName);
      } catch (error) {
        console.error(error);
        this.evolutionData = null;
      }
    },
  },
};
</script>