<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Pomodoro</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true" class="">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Pomodoro</ion-title>
        </ion-toolbar>
      </ion-header>

      <PomodoroCycle />
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
} from "@ionic/vue";
import PomodoroCycle from "@/components/Pomodoro/PomodoroCycle.vue";
export default defineComponent({
  name: "PomPage",
  components: {
    IonHeader,
    IonToolbar,
    IonTitle,
    IonContent,
    IonPage,
    PomodoroCycle,
  },
});
</script>
<style scoped></style>
