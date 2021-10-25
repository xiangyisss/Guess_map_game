<template>
  <div class="nav_bar">
    <div class="countryname_flag">
      <h4>
        Find this country <span>{{ countryName.countryName }}</span>
      </h4>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  props: { countryName: String },
});
</script>

<style scoped>
.nav_bar {
  display: flex;
  justify-content: space-around;
  align-items: center;
}
span {
  color: rgb(241, 234, 234);
}
</style>
