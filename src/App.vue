<template>
  <div id="app container" >
    <div class="row">
      <div class="col-md-6 col-12">
        <reactive-bar-chart :options="options"  :chart-data="chartData"></reactive-bar-chart>
      </div>
      <div class="col-md-6 col-12">
        <div class="col-12 px-2">
          Cumulative rows saved till today
        </div>
        <div class="col-12 px-2 text-muted">
          Tap to toggle line chart selection
        </div>
        <line-chart :options="options"  :chart-data="formattedLineChartData"> </line-chart>
      </div>
      <div class="row">
        <div class="col-md-6 col-12">
          <div class="row px-md-0 px-2">
            <div class="col-12 mx-2 mt-4">
              History
            </div>
            <div class="col-12">
              <scores-table :wordleScores='wordleScores' />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CONSTANTS from "./store/constants"
import ReactiveBarChart from "./components/ReactiveBarChart.js";
import LineChart from "./components/LineChart";
import ScoresTable from './components/ScoresTable.vue';

export default {
  name: "App",
  components: {
    ReactiveBarChart,
    LineChart,
    ScoresTable
  },
  data() {
    return {
      chartData: {},
      playerTotals : {},
      formattedLineChartData: {},
      index: 0,
      wordleScores:CONSTANTS.scores,
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
      this.generateBarChartData()
      this.generateLineChartData()
    },
    generateBarChartData(){
      let playerAverages = []

      this.playerTotals = Object.entries(this.playerTotals)
        .sort(([,a],[,b]) => a-b)
        .reduce((r, [k, v]) => ({ ...r, [k]: v }), {});

      Object.values(this.playerTotals).forEach((total)=> {
        playerAverages.push((total/this.index).toFixed(2))
      })

      this.chartData = {
        labels: Object.keys(this.playerTotals),
        datasets: [
          {
            label: "Wordle average guess per day",
            backgroundColor: ['#AAE2C3', '#F4E8AB', '#FFC1C1', '#FFC1C1'],
            data: playerAverages
          }
        ]
      };
    },
    generateLineChartData() {
      let lineChartData = {
        lineChartLabels: [],
        savesPerDayCumulative: {
          AM: [],
          SJ: [],
          GM: [],
          AJ: []
        }
      }
      this.wordleScores.forEach((score) => {
        lineChartData.lineChartLabels.push(score.wordleDay)
        lineChartData.savesPerDayCumulative.AM.push(this.getTotalSavesTillDate(lineChartData.savesPerDayCumulative.AM, score.playerScores.AM))
        lineChartData.savesPerDayCumulative.SJ.push(this.getTotalSavesTillDate(lineChartData.savesPerDayCumulative.SJ, score.playerScores.SJ))
        lineChartData.savesPerDayCumulative.GM.push(this.getTotalSavesTillDate(lineChartData.savesPerDayCumulative.GM, score.playerScores.GM))
        lineChartData.savesPerDayCumulative.AJ.push(this.getTotalSavesTillDate(lineChartData.savesPerDayCumulative.AJ, score.playerScores.AJ))
      })
      console.log(lineChartData)
      this.formattedLineChartData = {
        labels: lineChartData.lineChartLabels,
        datasets: [
          {
            data: lineChartData.savesPerDayCumulative.AM,
            label: "AM",
            borderColor: "#3e95cd",
            fill: false
          },
            {
              data: lineChartData.savesPerDayCumulative.AJ,
              label: "AJ",
              borderColor: "#8e5ea2",
              fill: false
            },
            {
              data: lineChartData.savesPerDayCumulative.GM,
              label: "GM",
              borderColor: "#3cba9f",
              fill: false
            },
            {
              data: lineChartData.savesPerDayCumulative.SJ,
              label: "SJ",
              borderColor: "#e8c3b9",
              fill: false
            }
        ]
      }
    },
    getTotalSavesTillDate(currentScoresArray, score) {
      if(currentScoresArray.length > 0) {
        return 6 - score + currentScoresArray[currentScoresArray.length - 1]
      } else
      return 6 - score
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
