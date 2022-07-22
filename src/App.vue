<template>
  <News :apiKey="apiKey" />
</template>

<script>
import News from "./components/News.vue";

export default {
  name: "App",
  data: () => {
    return {
      apiKey: process.env.VUE_APP_NEWS_API_KEY,
    };
  },
  components: {
    News,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
