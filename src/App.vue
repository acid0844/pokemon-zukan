<template>
  <div id="app">
    <PokemonList />
  </div>
</template>

<script>
import PokemonList from './components/PokemonList.vue';

export default {
  components: {
    PokemonList,
  },
  data() {
    return {
      // ... 他のデータ
      selectedPokemon: null
    };
  },
  methods: {
    showDetails(pokemon) {
      this.selectedPokemon = pokemon;
    },
    closeModal() {
      this.selectedPokemon = null;
    }
  }
}
</script>
