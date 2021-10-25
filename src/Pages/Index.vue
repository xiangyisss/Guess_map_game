<template>
  <h1>Worldmap Quiz Game</h1>
  <RandomCountryName :countryName="randomCountryName" />
  <WorldMap @randomCountryName="recieveRandomCountryName" />
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import WorldMap from "./MapComponent/WorldMap.vue";
import RandomCountryName from "./NavComponent/RandomCountryName.vue";

export default defineComponent({
  components: { RandomCountryName, WorldMap },
  name: "Home",
  setup() {
    const randomCountryName = ref("");
    const recieveRandomCountryName = (countryName: any) => {
      randomCountryName.value = countryName;
      return randomCountryName.value;
    };
    return {
      WorldMap,
      recieveRandomCountryName,
      randomCountryName,
    };
  },
});
</script>

<style scoped>
h1 {
  padding: 0.5rem;
  margin-bottom: 1rem;
}
</style>
