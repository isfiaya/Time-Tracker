<template>
  <div class="container">
    <Header
      :totalTime="totalTime"
      :formattedTime="formattedTime"
      :laps="laps"
      @start="start"
      @stop="stop"
      @removeLaps="removeLaps"
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
        :lapDateStart="lap.DateStart"
        :lapDateEnd="lap.DateEnd"
        :lapFormattedTime="lap.FormattedTime"
      />
    </main>
    <Footer :totalTime="totalTime" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import axios from "axios";
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
        // tICK FUNCTION UPDATE currentTimer EACH 1 SECOND
        this.tick();
        this.timerState = "running";
        const d = new Date();
        this.dateStart = d.toLocaleTimeString();
        // WHEN showCurrentPeriod = True UPDATE UI TO SHOW SECTION FOR Current Period
        this.showCurrentPeriod = true;
      }
    },
    tick() {
      this.ticker = setInterval(() => {
        this.currentTimer++;
        this.formattedTime = this.formatTime(this.currentTimer);
      }, 1000);
    },
    // RETURN  THE SECONDS ELAPSED ON THE FORMAT TIME
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
        this.id = this.laps.length + 1;
        this.postLap();
        this.currentTimer = 0;
        this.formattedTime = "00:00:00";
        this.timerState = "stopped";
        this.showCurrentPeriod = false;
        this.totalTimer();
      }
    },
    // REQUEST SEND DATA TO THE SERVER
    postLap() {
      axios
        .post("https://time-tracker-tekab.herokuapp.com/laps", {
          id: this.id,
          DateStart: this.dateStart,
          DateEnd: this.dateEnd,
          FormattedTime: this.formattedTime,
          seconds: this.currentTimer,
        })
        .then((res) => {
          console.log(res.data);
        })
        .then(() => this.getLap())
        .catch((err) => console.log(err));
    },
    // REQUEST GET DATA FROM THE SERVER
    getLap() {
      axios
        .get("https://time-tracker-tekab.herokuapp.com/laps")
        .then((res) => (this.laps = res.data))
        .then(() => console.log(this.laps))
        .then(() => this.totalTimer());
    },
    // REQUEST DELETE DATA FROM THE SERVER
    removeLaps() {
      if (this.timerState === "stopped" && this.laps.length) {
        axios
          .delete("https://time-tracker-tekab.herokuapp.com/laps/delete")
          .then((res) => console.log(res.data))
          .then(() => (this.laps = []))
          .then(() => this.totalTimer());
      }
    },
    totalTimer() {
      // IF THERE A SERVAL LAPS RETURN TOTAL
      if (this.laps.length) {
        const total = this.laps.reduce((a: any, b: any) => ({
          seconds: a.seconds + b.seconds,
        }));
        const totalPeriodAndCurrent = total.seconds + this.currentTimer;
        this.totalTime = this.formatTime(totalPeriodAndCurrent);
      } else {
        // IF THERE ONE LAP RETURN CURRENT TIMER
        this.totalTime = this.formatTime(this.currentTimer);
      }
    },
  },
  watch: {
    currentTimer: function () {
      this.totalTimer();
    },
  },
  created() {
    this.getLap();
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