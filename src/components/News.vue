<template>
  <div class="container">
    <div class="news-list">
      <h1 style="text-align: left">News Feed</h1>
      <div class="searchbar">
        <form @submit.prevent="fetchSearchNews">
          <input type="text" placeholder="Search..." v-model="searchword" />
        </form>
      </div>
      <div class="news-item" v-for="(item, index) in items" v-bind:key="index">
        <div>
          <img
            v-if="item.urlToImage"
            :src="item.urlToImage"
            alt=""
            class="image"
            @click="navTo(item.url)"
          />
        </div>
        <div>
          <div @click="navTo(item.url)" class="item-url">
            {{ item.title }}
          </div>
          <div>
            <p class="item-description">
              {{ item.description }}
            </p>
          </div>
        </div>
      </div>
    </div>
    <div ref="infinitescroll" id="scroll-items"></div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: ["apiKey"],
  data: () => {
    return {
      apiUrl: "",
      currentPage: 1,
      totalResults: 0,
      maxPerPage: 10,
      items: [],
      country: "us",
      pageSize: 3,
    };
  },
  computed: {
    pageCount() {
      return Math.ceil(this.totalResults / this.maxPerPage);
    },
  },
  methods: {
    navTo(url) {
      window.open(url);
    },
    fetchSearchNews() {
      if (this.searchword !== "") {
        this.apiUrl =
          "https://newsapi.org/v2/everything?q=" +
          this.searchword +
          "&pageSize=" +
          this.maxPerPage +
          "&apiKey=" +
          this.apiKey;

        this.currentPage = 1;
        this.items = [];
        this.loadData();
      } else {
        this.fetchNews();
      }
    },

    //https://newsapi.org/v2/everything?q=bitcoin&apiKey=5e25084f98b74d969d694da2ccf17e37
    fetchNews() {
      this.apiUrl =
        "https://newsapi.org/v2/top-headlines?country=" +
        this.country +
        "&pageSize=" +
        this.maxPerPage +
        "&apiKey=" +
        this.apiKey;

      this.currentPage = 1;
      this.items = [];

      this.loadData();
    },
    loadData() {
      let url = this.apiUrl + "&page=" + this.currentPage;
      axios
        .get(url)
        .then((response) => {
          console.log("---data----", response.data);
          this.totalResults = response.data.totalResults;
          console.log("this.totalResults ", this.totalResults);
          response.data.articles.forEach((element) => {
            //console.log("----element---", element);
            this.items.push(element);
          });
          //console.log("---articles----", this.items);
        })
        .catch((e) => {
          console.log("error", e);
        });
    },
    scrollItems() {
      const observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
          if (
            entry.intersectionRatio > 0 &&
            this.currentPage < this.pageCount
          ) {
            this.currentPage += 1;
            this.loadData();
          }
        });
      });
      observer.observe(this.$refs.infinitescroll);
    },
  },
  created() {
    this.fetchNews();
  },
  mounted() {
    this.scrollItems();
  },
};
</script>

<style  scoped>
.container {
  background-color: white;
  padding-top: 20px;
  border: 2px solid #f2f2f2;
}
.news-list {
  margin-right: auto;
  margin-left: auto;
  width: 50%;
}
.news-item {
  display: grid;
  grid-template-columns: 200px auto 40px;
  grid-template-rows: 100px;

  padding-bottom: 20px;
}
.image {
  width: 100%;
  height: 100%;
}
.searchbar {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 30px;
}
.item-url {
  font-weight: bold;
  font-size: large;
  display: flex;
  padding-left: 20px;
  text-align: left;
  cursor: pointer;
}
.item-description {
  display: flex;
  font-size: small;
  padding-left: 20px;
  text-align: left;
  margin-top: 10px;
}
.content {
  display: flex;
  margin-bottom: 30px;
  float: left;
  margin: 0;
}
</style>