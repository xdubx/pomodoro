<template>
  <div class="content">
    <h1>What is 🍅?</h1>
    <p></p>

    <h1>Why use 🍅?</h1>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "ExploreContainer",
  props: {
    name: String,
  },
  data() {
    return {
      open: false,
    };
  },
});
</script>

<style scoped>
.content {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
</style>
