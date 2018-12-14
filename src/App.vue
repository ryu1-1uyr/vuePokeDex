<template>
  <div id="app">
    <img :src="img" v-on:mouseover="over()" v-on:mouseleave="leave()" v-on:click="colored()">
    <h1>{{name}}</h1>
    <input type="text" v-model="name" placeholder="ポケモンの名前〜〜">
    <button type="button" v-on:click="setPokemonId()">ＢＯＴＡＮＮ</button>
    <p>{{ type }}</p>
    <p>{{ type2 }}</p>
    <p>おもさ : {{weight}}</p>
    <p>たかさ : {{height}}</p>
    <p>{{dexNumber}}</p>
    <h1 style="color:red;">{{ error }}</h1>
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
        name: "",
        type: "",
        type2: "",
        error: "",
        weight: "",
        height: "",
        img: "",
        front: "",
        back: "",
        front_shiny: "",
        back_shiny: "",
        dexNumber: "",
        pokemonData: "",
        url: "https://pokeapi.co/api/v2/pokemon/"
      }
    },
    methods : {
      getPokemon () {
        axios.get(this.url+this.dexNumber+"/")
          .then((res)=>{
            console.log(res["data"])

            this.type = res["data"]["types"][0]["type"]["name"]

            if (res["data"]["types"][1]) {
              this.type2 = res["data"]["types"][1]["type"]["name"]
            } else {
              console.log("unchi")
              this.type2 = ""
            }

            this.weight = res["data"]["weight"]
            this.height = res["data"]["height"]

            this.front = res["data"]["sprites"]["front_default"]
            this.back  = res["data"]["sprites"]["back_default"]

            this.front_shiny = res["data"]["sprites"]["front_shiny"]
            this.back_shiny  = res["data"]["sprites"]["back_shiny"]

            this.img = this.front
          })
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
          this.error = "存在しないポケモンです"
        } else {
          this.error = null
          this.getPokemon()
        }
      },
      over() {
        this.img = this.back
      },
      leave() {
        this.img = this.front
      },
      colored() {
        this.img = this.front_shiny
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
