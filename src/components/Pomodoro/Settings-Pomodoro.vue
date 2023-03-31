<template>
  <button @click="toggleModal()" class="setting-button">
    <ion-icon :icon="settings" />
  </button>

  <div v-if="open" class="modal">
    <h1>Settings</h1>
    <div class="settings">
      <div class="settings__input">
        <label for="endSound">Notifier Sound</label>
        <select name="sounds" v-model="endSound" id="endSound">
          <option value="mystery-alert">Mystery</option>
          <option value="retro">Retro</option>
          <option value="rooster">Rooster</option>
          <option value="ding-ding">Ding Ding</option>
          <option value="dong">Dong</option>
        </select>
      </div>
      <div class="settings__input">
        <label for="color">Theme Highlight color</label>
        <input type="color" v-model="color" id="color" />
      </div>
      <div class="settings__input">
        <label for="themeColor">Theme Base color</label>
        <select name="themeColor" v-model="themeColor" id="themeColor">
          <option value="mystery-alert">White</option>
          <option value="retro">Dark</option>
        </select>
      </div>
      <div class="settings__input">
        <label for="color">Bonus Round</label>
        <input type="checkbox" name="bonus" v-model="bonus" value="1" />
      </div>
      <div class="settings__input">
        <label for="color">Auto Start Timer</label>
        <input
          type="checkbox"
          name="auto-timer"
          v-model="autoStart"
          value="1"
        />
      </div>
    </div>
    <button @click="save()" class="settings__btn transparent-button">
      Save
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { settings } from "ionicons/icons";
import { IonIcon } from "@ionic/vue";
// eslint-disable-next-line
const click = require(`@/assets/double-click.wav`);

export default defineComponent({
  name: "Settings-Pomodoro",
  components: { IonIcon },
  inheritAttrs: false,

  data() {
    return {
      settings,
      endSound: "",
      color: "",
      themeColor: "",
      bonus: false,
      autoStart: false,
      open: false,
    };
  },

  computed: {},
  watch: {},
  methods: {
    save() {
      localStorage.setItem("sound", this.endSound.toString());
      localStorage.setItem("color", this.color.toString());
      localStorage.setItem("themeColor", this.themeColor.toString());
      localStorage.setItem("bonus", this.bonus.toString());
      localStorage.setItem("autoStart", this.autoStart.toString());
      // load sound
      this.open = false;
    },

    toggleModal() {
      this.open = !this.open;
    },

    loadItems() {
      const sound = localStorage.getItem("sound");
      const color = localStorage.getItem("color");
      const themeColor = localStorage.getItem("themeColor");
      const bonus = localStorage.getItem("bonus");
      const autoStart = localStorage.getItem("autoStart");
      if (sound && color && bonus && autoStart && themeColor) {
        this.endSound = sound;
        this.color = color;
        this.themeColor = themeColor;
        this.bonus = bonus === "true";
        this.autoStart = autoStart === "true";
      }
    },

    openModal() {
      this.open = true;
    },
  },
  mounted() {
    this.loadItems();
  },
});
</script>

<style scoped lang="scss">
@import "../../theme/global.scss";
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  transform: translateX(-50%);
  padding: 20px;
  background-color: black;

  h1 {
    margin: 10px 0;
  }
}

.settings {
  &__input {
    min-width: 300px;
    padding: 10px 0;
    display: flex;
    justify-content: space-between;
  }
  &__btn {
    float: right;
    margin-top: 10px;
  }
}

.setting-button {
  position: absolute;
  right: 20px;
  top: 20px;
  padding: 10px 10px 9px 10px;
  border-radius: 20px;
}
</style>
