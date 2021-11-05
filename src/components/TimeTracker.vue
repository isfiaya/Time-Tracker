<template>
  <div class="container">
    <Header
      :totalTime="totalTime"
      :formattedTime="formattedTime"
      :laps="laps"
      @start="start"
      @stop="stop"
    />
    <main>
      <section class="current_period" v-if="showCurrentPeriod">
        <div class="period_desc">
          <h2>Period {{laps.length + 1}}</h2>
          <p>Current Period</p>
        </div>
        <div class="period_time">
          <p>Started {{dateStart}}</p>
          <p>{{formattedTime}}</p>
        </div>
      </section>
      <Period
        v-for="(lap,index) in laps.slice().reverse()"
        :key="index"
        :lapId="lap.id"
        :lapDateStart="lap.dateStart"
        :lapDateEnd="lap.dateEnd"
        :lapFormattedTime="lap.formattedTime"
      />
    </main>
    <Footer :totalTime="totalTime" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Header from "@/components/Header.vue";
import Period from "@/components/Period.vue";
import Footer from "@/components/Footer.vue";
export default defineComponent({
  name: "TimeTracker",
  components: {
    Header,
    Footer,
    Period,
  },
  data() {
    return {
      id: 0,
      totalTime: 0 as number | string,
      showCurrentMonth: "",
      showCurrentDate: "" as number | string,
      formattedTime: "00:00:00" as string,
      timerState: "stopped",
      ticker: 0 as number,
      currentTimer: 0,
      dateStart: "" as number | string,
      dateEnd: "" as number | string,
      laps: [] as any,
      showCurrentPeriod: false,
    };
  },
  methods: {
    start() {
      if (this.timerState !== "running") {
        this.tick();
        this.timerState = "running";
        const d = new Date();
        this.dateStart = d.toLocaleTimeString();
        this.showCurrentPeriod = true;
      }
    },
    tick() {
      this.ticker = setInterval(() => {
        this.currentTimer++;
        this.formattedTime = this.formatTime(this.currentTimer);
      }, 1000);
    },
    formatTime(seconds: number) {
      let measuredTime = new Date(0);
      measuredTime.setSeconds(seconds);
      let MHSTime = measuredTime.toISOString().substr(11, 8);
      return MHSTime;
    },
    stop() {
      if (this.timerState === "running") {
        window.clearInterval(this.ticker);
        const d = new Date();
        this.dateEnd = d.toLocaleTimeString();
        this.id++;
        this.laps.push({
          seconds: this.currentTimer,
          formattedTime: this.formatTime(this.currentTimer),
          dateEnd: this.dateEnd,
          dateStart: this.dateStart,
          id: this.id,
        });
        console.log(this.laps);
        this.currentTimer = 0;
        this.formattedTime = "00:00:00";
        this.timerState = "stopped";
        this.showCurrentPeriod = false;
        this.totalTimer();
      }
    },
    totalTimer() {
      if (this.laps.length) {
        const total = this.laps.reduce((a: any, b: any) => ({
          seconds: a.seconds + b.seconds,
        }));
        const totalPeriodAndCurrent = total.seconds + this.currentTimer;
        this.totalTime = this.formatTime(totalPeriodAndCurrent);
      }
    },
  },
  watch: {
    currentTimer: function () {
      this.totalTimer();
    },
  },
});
</script>

<style lang="scss" scoped>
.container {
  max-width: 800px;
  margin: auto;
}
.current_period {
  padding: 1rem;
  display: flex;
  flex-direction: column;
  border-top: solid 1px #60b669;
  border-bottom: solid 1px #60b669;
  gap: 1rem;
  .period_desc {
    h2 {
      margin: 0 0 0.25rem 0;
      color: #6d7783;
      font-size: 1rem;
    }
    p {
      margin: 0;
      color: #999999;
      font-weight: 500;
    }
  }
  .period_time {
    display: flex;
    p {
      margin-right: 2rem;
      color: #6b7581;
    }
    p:nth-child(2) {
      color: #60b669;
      font-weight: bold;
    }
  }
}
@media (min-width: 640px) {
  .current_period {
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }
}
</style>