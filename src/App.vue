<template>
  <div id="app">
    <WeatherApp />
  </div>
</template>

<script>
import WeatherApp from "./components/WeatherApp.vue";

export default {
  name: "App",
  components: {
    WeatherApp
  }
};
</script>

<style lang="scss">
body {
  margin: 0;
  padding: 0;
}
* {
  box-sizing: border-box;
}
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: "Comfortaa", cursive;
  width: 100%;


}
</style>
