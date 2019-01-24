<template>
  <div id="app">
    <img :src="img" v-on:mouseover="over()" v-on:mouseleave="leave()" v-on:click="colored()">
    <h1>{{name}}</h1>
    <VueSingleSelect
      :options="pokeList"
      v-model="name"
    ></VueSingleSelect>

    <button type="button" v-on:click="setPokemonId()">ＢＯＴＡＮＮ</button>
    <p>{{ type }}</p>
    <p>{{ type2 }}</p>
    <p>おもさ : {{weight}}</p>
    <p>たかさ : {{height}}</p>
    <p>{{dexNumber}}</p>
    <h1 style="color:red;">{{ error }}</h1>

    <div style="background-color: gainsboro;">
      <h3>能力値</h3>
      <RadarChart :data="Radardata" :options="options" ref="radarchart"></RadarChart>
    </div>


  </div>
</template>

<script>

  import axios from 'axios'
  import poke from 'pokemon'
  import Vuetify from 'vuetify'
  import VueSingleSelect from "vue-single-select";



  import RadarChart from '@/components/chart/RadarChart.vue'

  console.log(poke.getName(1,'ja'))

  let pokeList = poke.all('ja')

  export default {
    name: 'App',
    components: {
      RadarChart,
      VueSingleSelect
    },
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
        url: "https://pokeapi.co/api/v2/pokemon/",
        pokeList : poke.all('ja'),

        Radardata: {
          labels: ["HP", "攻撃", "防御","素早さ","特防", "特攻"],
          datasets: [{
            label: "ポケモンの能力値",
            data: [92, 72, 86, 74, 86,100],
            backgroundColor: 'RGBA(225,95,150, 0.5)',
            borderColor: 'RGBA(225,95,150, 1)',
            borderWidth: 1,
            pointBackgroundColor: [
              'rgba(130, 255, 100, 0.7)',//green
              'rgba(255, 100, 130, 0.7)',//red
              'rgba(100, 130, 255, 0.7)',//blue
              'rgba(153, 255, 255, 0.7)',//light blue
              'rgba(153, 153, 153, 0.7)',//grey
              'rgba(230, 210, 85,  0.7)',//yellow
            ]
          }]},
        // グラフオプション
        options : {
          title: {
            display: true,
          },scale:{
            ticks:{
              suggestedMin: 0,
              suggestedMax: 100,
              stepSize: 10,
              }
            }
          }

      }
    },
    methods : {
      async getPokemon () {

        const res = await axios.get(this.url+this.dexNumber+"/");

          if (res.status !== 200) {
            this.error = "データが取得できませんでした"
          } else {

            console.log(res["data"]);


            this.type = res["data"]["types"][0]["type"]["name"]

            if (res["data"]["types"][1]) {
              this.type2 = res["data"]["types"][1]["type"]["name"]
            } else {
              this.type2 = ""
            }

            this.weight = res["data"]["weight"]
            this.height = res["data"]["height"]

            this.front = res["data"]["sprites"]["front_default"]
            this.back  = res["data"]["sprites"]["back_default"]

            this.front_shiny = res["data"]["sprites"]["front_shiny"]
            this.back_shiny  = res["data"]["sprites"]["back_shiny"]

            this.Radardata.datasets[0].data[0] = res["data"]["stats"][5]["base_stat"],// h
            this.Radardata.datasets[0].data[1] = res["data"]["stats"][4]["base_stat"],// a
            this.Radardata.datasets[0].data[2] = res["data"]["stats"][3]["base_stat"],// b
            this.Radardata.datasets[0].data[3] = res["data"]["stats"][0]["base_stat"],// s
            this.Radardata.datasets[0].data[4] = res["data"]["stats"][2]["base_stat"],// c
            this.Radardata.datasets[0].data[5] = res["data"]["stats"][1]["base_stat"],// d

              //S D C B A H json

              //["HP", "攻撃", "防御","素早さ","特防", "特攻"], dataset


              this.img = this.front

          }

          await this.update()
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
          this.error = "存在しないポケモンです。"
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
      },
      update () {
        this.$refs.radarchart.update()
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
