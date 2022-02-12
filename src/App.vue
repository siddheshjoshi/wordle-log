<template>
  <div id="app container" >
    <div class="row">
      <div class="col-md-6 col-12">
        <reactive-bar-chart :options="options"  :chart-data="chartData"></reactive-bar-chart>
      </div>
      <div class="col-md-6 col-12">
        <div class="row px-md-0 px-2">
          <div class="col-12">
            Log...
          </div>
          <div class="col-12">
            <div v-for="(log, index) in wordleScores" :key="index">
              Wordle day: {{ log.wordleDay }} --> Player scores: {{log.playerScores}}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ReactiveBarChart from "./components/ReactiveBarChart.js";
export default {
  name: "App",
  components: {
    ReactiveBarChart
  },
  data() {
    return {
      chartData: {},
      playerTotals : {},
      index: 0,
      wordleScores:[
        {
          wordleDay: "231",
          playerScores: {
            "GM":4,
            "AM":2,
            "AJ":1,
            "SJ":6
          }
        },
        {
          wordleDay: "231",
          playerScores: {
            "GM":1,
            "AM":2,
            "AJ":2,
            "SJ":1
          }
        },
        {
          wordleDay: "231",
          playerScores: {
            "GM":2,
            "AM":2,
            "AJ":2,
            "SJ":2
          }
        }
      ],
      options: {
        scales: {
          yAxes: [
              {
              ticks: {
                  beginAtZero: true
              }
          }]
        },
        responsive: true,
        maintainAspectRatio: false
        }
    };
  },
  methods: {
    generateData() {
      this.getScores()
      let playerAverages = []
      Object.values(this.playerTotals).forEach((total)=> {
        playerAverages.push((total/this.index).toFixed(2))
      })
      this.chartData = {
        labels: Object.keys(this.playerTotals),
        datasets: [
          {
            label: "Wordle average",
            backgroundColor: "#f87979",
            data: playerAverages
          }
        ]
      };
    },
    getScores() {
      let playerTotals = {"AJ": 0,"SJ":0,"AM":0,"GM":0}
      let index = 0
      this.wordleScores.forEach((data) => {
        index++
        for (let [key, value] of Object.entries(data.playerScores)) {
          playerTotals[key] += value
        }
      })
      console.log(playerTotals)
      this.index = index
      this.playerTotals = playerTotals
    }
  },
  mounted() {
    this.generateData()
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
