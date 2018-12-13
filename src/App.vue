<template>
  <div id="app">
    <img src="../Pokemon-DB/img/025Pikachu.png">
    <router-view/>
  </div>
</template>

<script>


  import pokeJSON from '../Pokemon-DB/pokedex'

  console.log(pokeJSON);

  export default {
    name: 'App',
    data () {
      return {
        name: "",
        type: "",
        denNumber: ""
      }
    },
    methods: {
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
