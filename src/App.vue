<template>
  <div id="app" >
    <div class="container">
      <VueSingleSelect class="search" :options="pokeList" v-model="name" />
      <button type="button" class="button" v-on:click="setPokemonId()">検索</button>
      <button type="button" class="button" v-on:click="randomGetPokemon()">ランダム</button>

      <div class="row">
        <div class="col text">
          <h2 v-if="dexNumber">{{dexNumber}}</h2>
          <img :src="img" v-on:mouseover="over()" v-on:mouseleave="leave()" v-on:click="colored()">
          <h1>{{name}}</h1>

          <p>{{ type  }}<span v-if="type2"> , {{ type2 }}</span> </p>

          <p>おもさ : {{weight}}</p>
          <p>たかさ : {{height}}</p>
          <h1 style="color:red;" v-if="error">{{ error }}</h1>
        </div>

        <div class="col">
          <RadarChart :data="Radardata" :options="options" ref="radarchart"></RadarChart>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import poke from 'pokemon'
import VueSingleSelect from "vue-single-select";
import RadarChart from '@/components/chart/RadarChart.vue'

const firstPokemon = poke.random('ja')
const typeList = [
  {'en':'normal','ja':'ノーマル'},
  {'en':'fire','ja':'ほのお'},
  {'en':'water','ja':'みず'},
  {'en':'grass','ja':'くさ'},
  {'en':'electric','ja':'でんき'},
  {'en':'ice','ja':'こおり'},
  {'en':'fighting','ja':'かくとう'},
  {'en':'poison','ja':'どく'},
  {'en':'ground','ja':'じめん'},
  {'en':'flying','ja':'ひこう'},
  {'en':'psychic','ja':'エスパー'},
  {'en':'bug','ja':'むし'},
  {'en':'rock','ja':'いわ'},
  {'en':'ghost','ja':'ゴースト'},
  {'en':'dragon','ja':'ドラゴン'},
  {'en':'dark','ja':'あく'},
  {'en':'steel','ja':'はがね'},
  {'en':'fairy','ja':'フェアリー'}
];


let pokeList = poke.all('ja');

export default {
  name: 'App',
  components: {
    RadarChart,
    VueSingleSelect
  },
  data () {
    return {
      name: firstPokemon,
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
      dexNumber: poke.getId(firstPokemon,'ja'),
      pokemonData: "",
      url: "https://pokeapi.co/api/v2/pokemon/",
      pokeList : poke.all('ja'),

      Radardata: {
        labels: ["HP", "攻撃", "防御","素早さ","特防", "特攻"],
        datasets: [{
          label: "ポケモンの能力値",
          data: [0, 0, 0, 0, 0, 0],
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
        let data = res["data"]
        this.type = typeList.filter(type => type['en'] == data["types"][0]["type"]["name"])[0]['ja']
        if (data["types"][1]) {
          this.type2 = typeList.filter(type => type['en'] == data["types"][1]["type"]["name"])[0]['ja']
        } else {
          this.type2 = ""
        }

        this.weight = data["weight"]
        this.height = data["height"]
        this.front = data["sprites"]["front_default"]
        this.back  = data["sprites"]["back_default"]
        this.front_shiny = data["sprites"]["front_shiny"]
        this.back_shiny  = data["sprites"]["back_shiny"]
        this.Radardata.datasets[0].data[0] = data["stats"][5]["base_stat"],// h
        this.Radardata.datasets[0].data[1] = data["stats"][4]["base_stat"],// a
        this.Radardata.datasets[0].data[2] = data["stats"][3]["base_stat"],// b
        this.Radardata.datasets[0].data[3] = data["stats"][0]["base_stat"],// s
        this.Radardata.datasets[0].data[4] = data["stats"][2]["base_stat"],// c
        this.Radardata.datasets[0].data[5] = data["stats"][1]["base_stat"],// d
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
    randomGetPokemon () {
      const randomPokemon = poke.random('ja')

      this.name = randomPokemon
      this.dexNumber = poke.getId(randomPokemon,'ja')

      this.getPokemon()
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
  },
  mounted: function(){
    this.getPokemon()
  },
}
</script>


<style>
#app {
  padding: 0;
  margin: 0;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /*margin-top: 60px;*/
  height: 100%;
}
.button {
  background-color: lightsteelblue;
  border-radius: 40px;
  /*width: 60px;*/
}
.search {
  display: flex;
  justify-content: center;
  align-items: center;
}
.text {
  position:relative;
  margin-top: 4%;
}
.text p h2 h1 img {
  position:absolute;

}
</style>
