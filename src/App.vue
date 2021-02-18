<template>
  <div id="app">
    <Map style="margin-bottom: 60px;" @userDirection="getUserDirection" />
    <RestaurantInfo :userDirections="userDirections" />
  </div>
</template>

<script>
import Map from "./components/Map.vue";
import RestaurantInfo from "./components/InfoMaps.vue";
export default {
  name: "App",
  components: {
    Map,
    RestaurantInfo,
  },
  data: () => {
    return {
      userDirections: {
        lat: 0,
        lng: 0,
      },
    };
  },
  methods: {
    getUserDirection(userDirection) {
      this.userDirections = userDirection;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-color: #a02a2a;
}
</style>
