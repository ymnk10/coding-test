<template>
  <div class="prefectures-area">
    <div v-for="prefecture in prefectures" :key="prefecture.id" class="prefecture">
      <label :for="prefecture.id">
        <input type="checkbox" :id="prefecture.id" :checked="prefecture.isChecked"
          @click="checkChart(prefecture.id, prefecture.name, prefecture.isChecked)" />
        {{ prefecture.name }}
      </label>
    </div>
  </div>
</template>

<script>
import axios from "axios";

const RESAS_API_KEY = "jdDjcInWuYMNd36UIahSWIPUgRg3kbAkgF1Ntd6P";

export default {
  data() {
    return {
      prefectures: []
    };
  },
  mounted() {
    this.initPrefectures();
  },
  methods: {
    getData: function (path) {
      const response = axios
        .get(`https://opendata.resas-portal.go.jp/api/v1/${path}`,
          {
            headers: { "X-API-KEY": RESAS_API_KEY }
          }
        );
      return response;
    },

    initPrefectures: async function () {
      const path = "prefectures";
      const response = await this.getData(path);
      this.prefectures = response.data.result.map(val => {
        return {
          id: val["prefCode"],
          name: val["prefName"],
          isChecked: false
        };
      });
    },

    drawChart: async function (id, name) {
      const path = `population/composition/perYear?cityCode=-&prefCode=${id}`;
      const response = await this.getData(path);
      const population = response.data.result.data[0].data.map(
        val => val["value"]
      );
      this.$emit("onAddSeries", id, name, population);
      this.prefectures[id - 1].isChecked = true;
    },

    deleteChart: function (id) {
      this.$emit("onDeleteSeries", id);
      this.prefectures[id - 1].isChecked = false;
    },

    checkChart: function (id, name, isChecked) {
      if (isChecked) {
        this.deleteChart(id);
      } else {
        this.drawChart(id, name);
      }
    }
  }
};
</script>

<style scoped>
.prefectures-area {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  font-size: 10px;
}

.prefectures {
  font-size: 15px;
}

label {
  cursor: pointer;
}
</style>