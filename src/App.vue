<template>
  <h1>Worldmap Quiz Game</h1>
  <!-- <Nav /> -->
  <WorldMap  />
</template>

<script lang="ts">
import { defineComponent } from "vue";
// import Nav from "./components/Nav.vue";
import WorldMap from "./components/WorldMap.vue";

export default defineComponent({
  name: "App",
  components: {
    WorldMap
  },
});
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
  text-align: center;
  color: #2c3e50;
  /* margin-top: 60px; */
}
</style>
