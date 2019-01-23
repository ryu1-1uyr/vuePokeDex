<script>
  import { Radar } from 'vue-chartjs'
  export default {
    name: 'RadarChart',
    extends: Radar,
    props: ['data', 'options'],
    mounted: function(){
      this.renderChart(this.data, this.options)
    },
    methods: {
      update () {
        this.renderChart(this.data, this.options)
      },
    },
    watch : {
      data : function(val) {
        this.renderChart(this.data, this.options)
      },
      options : function(val) {
        this.renderChart(this.data, this.options)
      }
    }
  }
</script>
