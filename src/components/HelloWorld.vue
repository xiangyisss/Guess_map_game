<template>
  <h1>Worldmap Quiz Game</h1>
   <Nav />
  <div class="map_area">
  
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Nav from "./Nav.vue";

export default defineComponent({
  components: { Nav },
  name: "HelloWorld",
  props: {
    msg: String,
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  margin-bottom: 1rem;
}
.map_area {
  width: 80%;
  height: 80vh;
  background-image: url('../assets/world.svg');
  background-repeat: no-repeat;
  background-size: contain;
  background-position: center;
  margin: auto;
}
.quiz_bar {
  width: 100%;
  height: 10%;
  background-color: rgb(186, 142, 228);
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 600;
}
.flag_img {
  width: 60px;
  height: 40px;
  background-color: #fff;
  margin-left: 1rem;
}

img {
  width: 100%;
  height: 100%;
}

/* svg {
   width="1009.6727"
   height="665.96301"
} */
</style>
