<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{name}}</h1>

    <input type="text" name="yourarea" autocomplete="on" list="tokyo"　v-model="name">
    <datalist id="tokyo">

      <div v-for="pokemon in pokeList">
        <option :value="pokemon" ></option>
      </div>

    </datalist>

    <br>

    getpoke<button type="button" v-on:click="getPokemon()" style="background-color: black;">取得</button>
    setpokeid<button type="button" v-on:click="setPokemonId()"></button>
    <p>{{dexNumber}}</p>
  </div>
</template>

<script>

  import axios from 'axios'
  import poke from 'pokemon'

  console.log(poke.getName(1,'ja'))

  const pokeList = poke.all('ja')

  export default {
    name: 'App',
    data () {
      return {
        name: "ピカチュウ",
        type: [],
        dexNumber: "25",
        pokemonData: "",
        url: "https://pokeapi.co/api/v2/pokemon/",
        pokeList: pokeList
      }
    },
    methods : {
      getPokemon () {
        axios.get(this.url+this.dexNumber+"/")
          .then((res)=>{console.log(res["data"])})
        // .then((json)=>{console.log(json)})
      },
      setPokemonId () {
        let flag = false

        for (let i of pokeList){
          if (this.name == i) {
            this.dexNumber = poke.getId(this.name, 'ja')
            flag = false
            break;
          } else {
            flag = true
          }

        }

        if (flag) {
          this.name = "存在しないポケモンです"
        }


      }
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
