<template>
  <div id="app">
    <img :src="img" v-on:mouseover="over()" v-on:mouseleave="leave()" v-on:click="colored()">
    <h1>{{name}}</h1>
    <input type="text" v-model="name" placeholder="ポケモンの名前〜〜" autocomplete="on" list="pokemons">
    <datalist id="pokemons">
      <option v-for="pokename in pokeList" :value="pokename" />
    </datalist>

    <button type="button" v-on:click="setPokemonId()">ＢＯＴＡＮＮ</button>
    <p>{{ type }}</p>
    <p>{{ type2 }}</p>
    <p>おもさ : {{weight}}</p>
    <p>たかさ : {{height}}</p>
    <p>{{dexNumber}}</p>
    <h1 style="color:red;">{{ error }}</h1>

    <div style="background-color: gainsboro;">
      <h3>円グラフを描画する</h3>
      <PieChart :data="pieChartData" :options="options"></PieChart>
    </div>

  </div>
</template>

<script>

  import axios from 'axios'
  import poke from 'pokemon'

  import PieChart from '@/components/chart/PieChart.vue'

  console.log(poke.getName(1,'ja'))

  let pokeList = poke.all('ja')

  export default {
    name: 'App',
    components: {
      PieChart
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

        pieChartData : {
          // ラベル
          labels: ["HP", "攻撃", "防御", "特攻","特防","素早さ"],
          // データ詳細
          datasets: [{
            label: '藩と人口',
            data: [100, 100, 200, 100,300,100],
            backgroundColor: [
              'rgba(130, 255, 100, 0.2)',//green
              'rgba(255, 100, 130, 0.2)',//red
              'rgba(100, 130, 255, 0.2)',//bule
              'rgba(230, 210, 85,  0.2)',//yellow
              'rgba(153, 153, 153, 0.2)',//grey
              'rgba(153, 255, 255, 0.2)'//light blue
            ]
          }]
        },
        // グラフオプション
        options : {
          title: {
            display: true,
            text: '藩と人口'
          },
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

            this.pieChartData.datasets[0].data[0] = res["data"]["stats"][5]["base_stat"],
            this.pieChartData.datasets[0].data[1] = res["data"]["stats"][4]["base_stat"],
            this.pieChartData.datasets[0].data[2] = res["data"]["stats"][3]["base_stat"],
            this.pieChartData.datasets[0].data[3] = res["data"]["stats"][2]["base_stat"],
            this.pieChartData.datasets[0].data[4] = res["data"]["stats"][1]["base_stat"],
            this.pieChartData.datasets[0].data[5] = res["data"]["stats"][0]["base_stat"],

            // console.log(this.options)


            this.img = this.front

          }

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
