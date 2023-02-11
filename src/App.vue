<template>
  <header class="title">
    <h1 class="app-title">PokeSearch!</h1>
    <h3>Type a pokemon and see the evolution chain!</h3>
    <form @submit.prevent="searchPokemon" v-on:click="isSelected = true">
      <input
        type="text"
        v-model="searchName"
        placeholder="What's your favorite pokemon?"
      />
      <button type="submit">Submit</button>
    </form>
    <div class="error-popup" v-if="error !== ''">
      <h1>We didn't found this pokemon.</h1>
      <h3>{{ error }}</h3>
    </div>
  </header>
  <main class="results" v-if="species !== []">
    <Card
      v-for="(pokemon, index) in species"
      :key="`poke` + index"
      :name="pokemon.name"
      :stats="pokemon.stats"
      :imageSrc="pokemon.sprite"
    />
  </main>
</template>

<script>
import axios from "axios";
import Card from "./components/Card.vue";

export default {
  data() {
    return {
      searchName: "",
      species: [],
      error: "",
    };
  },
  components: { Card },
  methods: {
    handleError(err) {
      this.error = err;
    },
    async searchPokemon() {
      this.error = "";
      this.species = [];
      this.fetchPokemon(this.searchName);
    },
    async fetchPokemon(pokemonName) {
      let pokemon = await axios
        .get(
          `https://pokeapi.co/api/v2/pokemon/${pokemonName
            .replace("'", "")
            .replace(/[.: ']+/g, "-")
            .toLowerCase()}`
        )
        .catch((err) => this.handleError(err));
      let specieURL = pokemon.data.species.url;
      let specie = await axios.get(specieURL);
      let evChain = await axios.get(specie.data.evolution_chain.url);
      let speciesNames = [];
      this.species = [];

      function getSpecies(obj) {
        for (let key in obj) {
          if (key == "species") {
            speciesNames.push(obj[key].name);
          }
          if (typeof obj[key] === "object") {
            getSpecies(obj[key]);
          }
        }
      }
      getSpecies(evChain.data.chain);
      for (let i = 0; i < speciesNames.length; i++) {
        let res = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${speciesNames[i]}`
        );
        let pokeData = res.data;
        let pokemon = {
          name: pokeData.species.name,
          sprite: pokeData.sprites.front_default,
          stats: pokeData.stats.map((i) => `${i.stat.name}: ${i.base_stat}`),
        };
        this.species.push(pokemon);
      }
      this.species.reverse();
    },
  },
};
</script>