<script setup>
import PokemonList from './components/PokemonList.vue';
</script>

<template>
  <div id="app">
    <PokemonList />
  </div>
</template>

<script>
import PokemonList from './components/PokemonList.vue';

export default {
  components: {
    PokemonList
  }
};
</script>