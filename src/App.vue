<template>
  <div id="app">
    <h1>触ってわかる！都道府県別の総人口推移グラフ</h1>
    <div class="prefectures-container">
      <h2>都道府県</h2>
      <Prefectures @onAddSeries="addSeries" @onDeleteSeries="deleteSeries" class="prefectures" />
      <h2>グラフ</h2>
    </div>
    <highcharts :options="options" />
  </div>
</template>

<script>
import { Chart } from "highcharts-vue";
import Prefectures from "./components/Prefectures.vue";

export default {
  components: {
    highcharts: Chart,
    Prefectures: Prefectures
  },
  data: function () {
    return {
      options: {
        title: {
          text: ''
        },
        legend: {
          type: "line",
          align: "right",
          verticalAlign: "top",
          layout: "vertical"
        },
        xAxis: {
          title: {
            text: "年度"
          },
          categories: [1960, 1965, 1970, 1975, 1980, 1985, 1990, 1995, 2000,
            2005, 2010, 2015, 2020, 2025, 2030, 2035, 2040, 2045]
        },
        yAxis: {
          title: {
            text: "人口数"
          }
        },
        series: []
      }
    };
  },
  methods: {
    addSeries: function (id, name, population) {
      this.options.series.push({
        id: id,
        name: name,
        data: population
      });
    },

    deleteSeries: function (id) {
      this.options.series = this.options.series.filter(val => val.id !== id);
    }
  }
};

</script>

<style scoped>
#app {
  max-width: 1024px;
  margin: 0 auto;
}

h1 {
  text-align: center;
  font-size: 18px;
  color: #364e96;
  padding: 0.5em 0;
  border-top: solid 3px #364e96;
  border-bottom: solid 3px #364e96;
}

h2 {
  font-size: 15px;
  padding: 0.5em;
  color: #494949;
  background: #f1f8fd;
  border-left: solid 5px #7db4e6;
}

.prefectures-container {
  margin-left: 4%;
}

.prefectures {
  font-size: 15px;
}

@media screen and (max-width: 400px) {

  h1 {
    font-size: 15px;
  }

  .prefectures-container {
    margin-left: 0;
  }
}

@media screen and (max-width: 425px) {

  h2 {
    font-size: 13px;
  }

  .prefectures-container {
    margin-left: 0;
  }

  .prefectures {
    font-size: 12px;
  }
}
</style>