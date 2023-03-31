<template>
  <div class="timer">
    <div class="timer__title transparent-button">Round: {{ currentRound }}</div>
    <div class="timer__img">
      <PomodoroTimeDrawer
        :paused="pause"
        :total="total"
        :elapsed="elapsed"
        style=""
      />
      <div class="timer__time">
        {{ currentRoundText }}
        <br />
        {{ timeLeft }}
      </div>
    </div>

    <div class="container">
      <button
        class="container__btn transparent-button"
        v-on:click="togglePomodoro()"
      >
        {{ pomodoroButton }}
      </button>
      <button
        class="container__btn transparent-button"
        v-on:click="skipState()"
      >
        Skip
      </button>
      <button
        class="container__btn transparent-button"
        v-on:click="resetRound()"
      >
        Reset
      </button>
      <MusicPlayer :stopCondition="pause" />
    </div>
    <Settings />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import PomodoroTimeDrawer from "./PomodoroTimeDrawer.vue";
import MusicPlayer from "../MusicPlayer.vue";
import Settings from "./Settings-Pomodoro.vue";
import { PomodoroState } from "../../types/pomodoro-state.enum";

// TODO: preload sound assets
// eslint-disable-next-line
const click = require(`@/assets/double-click.wav`);

export default defineComponent({
  components: { PomodoroTimeDrawer, Settings, MusicPlayer },
  name: "PomodoroCycle",
  inheritAttrs: false,

  data() {
    return {
      pomodoroButton: "Start",
      currentState: PomodoroState.WORK,
      currentRound: 0,
      timer: 0,
      pause: false,
      time: 0,
      total: 0,
      elapsed: 0,
      shortBreak: 5 * 60,
      longBreak: 15 * 60,
      work: 25 * 60,
      running: false,
    };
  },

  computed: {
    timeLeft(): string {
      return this.secondsToTime(this.total - this.elapsed);
    },
    currentRoundText(): string {
      switch (this.currentState) {
        case PomodoroState.WORK:
          return "Work";
        case PomodoroState.SHORTBREAK:
          return "Short Break";
        case PomodoroState.LONGBREAK:
          return "Long Break";
        case PomodoroState.RESET:
          return "";
        default:
          return "Work";
      }
    },
  },
  watch: {
    currentState(val) {
      this.running = false;
      this.elapsed = 0;
      switch (val) {
        case PomodoroState.WORK:
          this.total = this.work;
          break;
        case PomodoroState.SHORTBREAK:
          this.total = this.shortBreak;
          break;
        case PomodoroState.LONGBREAK:
          this.total = this.longBreak;
          break;
        default:
          this.total = 0;
          break;
      }
    },
  },
  mounted() {
    this.total = this.work;
    // TODO: load theme
  },
  methods: {
    togglePomodoro() {
      if (this.running) {
        this.stopTimer();
      } else {
        this.startTimer();
      }
      this.toggleButton();
    },
    toggleButton() {
      if (!this.running) {
        this.pomodoroButton = "Start";
      } else {
        this.pomodoroButton = "Stop";
      }
    },

    startTimer() {
      if (this.running) {
        return;
      }
      this.pause = false;
      this.timer = setInterval(() => {
        this.elapsed += 1;
        if (this.total - this.elapsed <= 0) {
          // stop timer
          // play sound
          this.playEndSound();
          clearInterval(this.timer);
          this.running = false;
          this.doPomodore();
          this.toggleButton();
        }
      }, 1000);
      this.running = true;
      this.playAudio("double-click");
    },

    stopTimer() {
      clearInterval(this.timer);
      this.running = false;
      this.pause = true;
      this.playAudio("double-click");
    },

    skipState() {
      clearInterval(this.timer);
      this.playAudio("double-click");
      this.pomodoroButton = "Start";
      this.running = false;
      this.pause = false;
      this.doPomodore();
    },

    resetRound() {
      clearInterval(this.timer);
      const state = this.currentState;
      this.currentState = PomodoroState.RESET;
      setTimeout(() => {
        switch (state) {
          case PomodoroState.WORK:
            this.currentState = PomodoroState.WORK;
            break;
          case PomodoroState.SHORTBREAK:
            this.currentState = PomodoroState.SHORTBREAK;
            break;

          case PomodoroState.LONGBREAK:
            this.currentState = PomodoroState.LONGBREAK;
            break;

          default:
            break;
        }
      }, 20);
      this.running = true;
      this.pomodoroButton = "Start";
    },

    updateTotalTime(time: number) {
      this.total = time;
    },

    secondsToTime(time: number) {
      const m = Math.floor((time % 3600) / 60)
          .toString()
          .padStart(2, "0"),
        s = Math.floor(time % 60)
          .toString()
          .padStart(2, "0");

      return m + ":" + s;
    },

    doPomodore() {
      if (this.currentState === PomodoroState.WORK) {
        if (this.currentRound !== 0 && this.currentRound % 4 === 0) {
          this.currentState = PomodoroState.LONGBREAK;
        } else {
          this.currentState = PomodoroState.SHORTBREAK;
        }
      } else {
        this.currentState = PomodoroState.WORK;
        this.currentRound += 1;
      }
    },

    // sound stuff
    playAudio(file: string) {
      try {
        // eslint-disable-next-line
        const audio = new Audio(require(`@/assets/${file}.wav`)); // TODO: get this from settings file
        audio.play();
        audio;
      } catch (e) {
        console.log(e);
      }
    },
    playEndSound() {
      const sound = localStorage.getItem("sound");
      try {
        // eslint-disable-next-line
        const audio = new Audio(require(`@/assets/${sound}.wav`)); // TODO: get this from settings file
        audio.play();
      } catch (e) {
        console.log(e);
      }
    },
  },
});
</script>

<style scoped lang="scss">
@import "../../theme/global.scss";
.timer {
  height: 100%;
  position: relative;
  display: flex;
  align-content: center;
  justify-content: center;
  flex-wrap: nowrap;
  flex-direction: column;
  align-items: center;

  &__title {
    margin-bottom: 50px;
  }
  &__img {
    height: 300px;
    position: relative;
  }
  &__time {
    position: absolute;
    margin-left: auto;
    margin-right: auto;
    top: 35%;
    left: 0;
    right: 0;
    text-align: center;
    font-size: 2.5em;
  }
}
.extendedMedia {
  height: 300px;
  width: 420px;
}
.container {
  display: flex;
  margin-top: 25px;
  &__btn {
    margin: 16px;
  }
}
</style>
