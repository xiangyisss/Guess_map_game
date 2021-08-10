<template>
    <div class="quiz_bar">
      Click on Canada
      <div class="flag_img"></div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
    name:"Nav",
    
})
</script>

<style scoped>
.quiz_bar {
  width: 100%;
  height: 8vh;
  background-color: rgb(186, 142, 228);
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 600;
 
}
.flag_img {
  width: 3.75rem;
  height: 2rem;
  background-color: whitesmoke;
  margin-left: 1rem;
}

img {
  width: 100%;
  height: 100%;
}
</style>