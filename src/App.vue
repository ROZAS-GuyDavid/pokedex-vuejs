<template>
  <div id="app">
    <div class="red darken-3">
      <img id="logo" src="./assets/logo-pokedex.png">
      <h1 class="title">Pokemon cards</h1>
    </div>
    <pokemons class="container"></pokemons>
  </div>
</template>

<script>
import Pokemons from './Pokemons.vue'
export default {
  name: 'app',
  components: { Pokemons },
}
</script>

