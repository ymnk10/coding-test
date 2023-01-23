<!-- <template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style> -->



<template>
  <div id="app">
    <h1>触ってわかる！都道府県別の総人口推移グラフ</h1>
    <div class="prefectures-container">
      <h2>都道府県</h2>
      <Prefectures @onAddSeries="addSeries" @onDeleteSeries="deleteSeries" />

    </div>
    <VueHighcharts :options="options" />
  </div>

</template>

<script src="https://code.highcharts.com/highcharts.js"></script>
<script>

import { Chart } from "highcharts-vue";
import Prefectures from "./components/Prefectures.vue";

export default {
  components: {
    VueHighcharts: Chart,
    Prefectures: Prefectures
  },
  data: function () {
    return {
      /* Highchartsのプロパティ */
      options: {
        /* seriesにオブジェクトを追加するとグラフに描画される */
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
    //  seriesに追加 
    addSeries: function (id, name, population) {
      this.options.series.push({
        id: id,
        name: name,
        data: population
      });
    },

    //  seriesから削除 
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
  font-size: 20px;
}

.prefectures-container {
  margin-left: 4%;
}

h2 {
  font-size: 17px;
}

@media screen and (max-width: 425px) {
  h1 {
    font-size: 18px;
  }

  h2 {
    font-size: 15px;
  }

  .prefectures-container {
    margin-left: 0;
  }
}
</style>