<template>
  <div id="app">
    <img src="../Pokemon-DB/img/025Pikachu.png">
    <input type="number" v-model="dexNumber" style="background-color: ghostwhite">
    <button v-on:click="setPokemon()" style="background-color: black"></button>
    <p>{{dexNumber}}</p>
    <p>{{pokemonData['ename']}}</p>
    <p>{{pokemonData['base']}}</p>
    <p>{{pokemonData}}</p>
    <router-view/>
  </div>
</template>

<script>


  import pokeJSON from '../Pokemon-DB/pokedex'
  import pokeType from '../Pokemon-DB/types'

  console.log(pokeJSON[0]);
  console.log(pokeType);

  export default {
    name: 'App',
    data () {
      return {
        name: "",
        type: "",
        dexNumber: "",
        pokemonData: ""
      }
    },
    methods: {
      setPokemon () {
        this.pokemonData = pokeJSON[this.dexNumber -1]
      },
      }

  }
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
