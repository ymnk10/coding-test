<!-- <template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank" rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div>
</template> -->

<!-- <script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script> -->

<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- <style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style> -->


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

// const ACCESS_TOKEN = process.env.VUE_APP_ACCESS_TOKEN;
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
    /* APIにアクセス */
    getData: function (path) {
      const response = axios
        .get(`https://opendata.resas-portal.go.jp/api/v1/${path}`,
          {
            headers: { "X-API-KEY": RESAS_API_KEY }
          }
        );
      return response;
    },

    /* 県の初期表示 */
    initPrefectures: async function () {
      const path = "prefectures";
      // try {
      const response = await this.getData(path);
      this.prefectures = response.data.result.map(val => {
        return {
          id: val["prefCode"],
          name: val["prefName"],
          isChecked: false
        };
      });
      // } catch (error) {
      //   console.error(error.message);
      // }
    },

    /* グラフを描画 */
    drawChart: async function (id, name) {
      const path = `population/composition/perYear?cityCode=-&prefCode=${id}`;
      // try {
      const response = await this.getData(path);
      const population = response.data.result.data[0].data.map(
        val => val["value"]
      );
      this.$emit("onAddSeries", id, name, population);
      this.prefectures[id - 1].isChecked = true;
      // } catch (error) {
      //   console.error(error.message);
      // }
    },

    /* グラフを削除 */
    deleteChart: function (id) {
      this.$emit("onDeleteSeries", id);
      this.prefectures[id - 1].isChecked = false;
      console.log(RESAS_API_KEY)//後で消す
        ;
    },

    /* グラフの表示非表示を切り替え */
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
  /* display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr; */
  /* padding: 6% 0; */
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}

.prefectures-area>* {
  min-width: 8%;
  flex-basis: 9%;
  background: #fff;
  font-size: 10px;
  text-align: center;
}

.prefectures {
  font-size: 15px;
}

label {
  cursor: pointer;
}

@media screen and (max-width: 425px) {
  .prefectures {
    font-size: 13px;
  }
}
</style>