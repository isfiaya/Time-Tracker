<template>
  <header>
    <h2>Today,{{showCurrentDate + ' ' + showCurrentMonth}}</h2>
    <div class="header_btns">
      <button class="header_btn_stop" @click="$emit('removeLaps')">
        <i class="uil uil-square-shape"></i>Remove all
      </button>
      <button class="header_btn_stop" @click="$emit('stop')">
        <i class="uil uil-square-shape"></i>Stop
      </button>
      <button class="header_btn_start" @click="$emit('start')">
        <i class="uil uil-stopwatch"></i>Start new
      </button>
      <p class="header_time" v-if="laps.length">{{totalTime}}</p>
      <p class="header_time" v-if="!laps.length">{{formattedTime}}</p>
    </div>
  </header>
</template>
<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  name: "Header",
  props: ["totalTime", "formattedTime", "laps"],
  data() {
    return {
      showCurrentMonth: "",
      showCurrentDate: "" as number | string,
    };
  },
  methods: {
    getCurrentDateTime() {
      let date = new Date();
      const month = date.toLocaleString("en-us", { month: "long" });
      this.showCurrentMonth = month;
      const currentDate = date.getDate();
      this.showCurrentDate = currentDate;
    },
  },
  created() {
    this.getCurrentDateTime();
  },
});
</script>
<style lang="scss" scoped>
header {
  display: flex;
  flex-direction: column;
  padding: 1rem;
  margin-bottom: 3rem;
  h2 {
    color: #415c73;
  }
  button {
    outline: none;
    margin-right: 2rem;
    padding: 0.5rem 0.75rem;
    background-color: white;
    border-radius: 0.25rem;
    font-size: 0.75rem;
    cursor: pointer;
    i {
      margin-right: 0.25rem;
      font-size: 1rem;
    }
  }
  button:active {
    transform: scale(0.9);
  }
  button:hover {
    background-color: #f0eaea;
  }
  p {
    color: #60b669;
    font-size: 1.25rem;
  }
}
.header_btns {
  display: flex;
  align-items: center;
  .header_btn_stop {
    border: 1px solid red;
    color: red;
  }
  .header_btn_start {
    border: 1px solid #089308;
    color: #089308;
  }
}
@media (min-width: 768px) {
  header {
    flex-direction: row;
    justify-content: space-between;
  }
}
</style>