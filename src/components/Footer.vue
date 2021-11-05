<template>
  <footer>
    <h3>
      Total
      <span>{{totalTime}}</span>
    </h3>
  </footer>
</template>
<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  name: "Footer",
  props: ["totalTime"],
});
</script>
<style lang="scss" scoped>
footer {
  margin-top: 4rem;
  font-weight: bold;
  padding: 0.25rem 1rem;
  border-top: solid 1px #e8e8e8;
  border-bottom: solid 1px #e8e8e8;
  h3 {
    color: #415c73;
  }
  span {
    color: #60b669;
    margin-left: 2rem;
  }
}
@media (min-width: 640px) {
  footer {
    text-align: end;
  }
}
</style>