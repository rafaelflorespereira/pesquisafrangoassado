<template>
  <div id="app">
    <Map />
    <InfoMaps />
  </div>
</template>

<script>
import Map from "./components/Map.vue";
import InfoMaps from "./components/InfoMaps";
export default {
  name: "App",
  components: {
    Map,
    InfoMaps,
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
  margin-top: 60px;
}
</style>
