<template>
  <MainPage />
</template>

<script>
import MainPage from "@/components/MainPage.vue";
export default {
  name: "App",
  components: {
    MainPage,
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  background-color: #589db0;
  min-height: 100vh;
}
</style>
