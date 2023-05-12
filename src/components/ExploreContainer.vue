<template>
  <div class="content">
    <h1>What is 🍅?</h1>
    <p class="content__text">
      xDubx's Pomodoro is a customizable pomodoro timer with music and task
      integration that works on desktop & mobile browser.
    </p>

    <h1>Why use 🍅?</h1>
    <p class="content__text">
      The Pomodoro Technique is a time management method developed by Francesco
      Cirillo in the late 1980s. It uses a kitchen timer to break work into
      intervals, typically 25 minutes in length, separated by short breaks. Each
      interval is known as a pomodoro, from the Italian word for tomato, after
      the tomato-shaped kitchen timer Cirillo used as a university student.
    </p>

    <h1>How use 🍅?</h1>
    <ol>
      <li>Add tasks to work on today</li>
      <li>Start timer and focus on the task for 25 minutes</li>
      <li>Take a break when the alarm ring</li>
      <li>Iterate until you finish the tasks</li>
    </ol>
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

<style lang="scss" scoped>
.content {
  display: flex;
  justify-content: center; // TODO: remove on mobile
  flex-direction: column;
  align-items: center;
  margin: 0 25px;
  height: 100%;

  &__text {
    max-width: 80%;
    text-align: center;
  }
}
</style>
