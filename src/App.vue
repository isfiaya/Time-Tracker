<template>
  <TimeTracker />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import TimeTracker from "./components/TimeTracker.vue";

export default defineComponent({
  name: "App",
  components: {
    TimeTracker,
  },
});
</script>

<style lang="scss">
#app {
  font-family: "Roboto", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
