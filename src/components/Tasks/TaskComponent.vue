<template>
  <div v-if="name">
    <div class="task">
      {{ name }} {{ round }}
      <div class="task__icon flex-center">
        <ion-icon
          v-if="showCheck !== false || showCheck === undefined"
          :icon="shieldCheckmarkOutline"
          class="task__icon--check"
          @click="$emit('checkItem', id)"
        />
        <ion-icon
          :icon="closeCircleOutline"
          class="task__icon--close"
          @click="$emit('deleteItem', id)"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { closeCircleOutline, shieldCheckmarkOutline } from "ionicons/icons";
import { defineComponent } from "@vue/runtime-core";
import { IonIcon } from "@ionic/vue";
export default defineComponent({
  props: {
    id: Number,
    name: String,
    round: Number,
    showCheck: Boolean,
  },
  data() {
    return {
      closeCircleOutline,
      shieldCheckmarkOutline,
    };
  },
  methods: {},
  emits: ["deleteItem", "checkItem"],
  components: { IonIcon },
});
</script>
<style lang="scss" scoped>
@import "../../theme/global.scss";

.task {
  padding: 15px;
  min-width: 350px; // TODO: update for small devices
  display: flex;
  justify-content: space-between;
  border: solid 1px;
  border-radius: 25px;
  word-break: break-all;
  &__icon {
    width: 45px;
    margin-left: 5px;
    &--check {
      color: var(--ion-color-success);
      &:hover {
        cursor: pointer;
      }
    }
    &--close {
      margin-left: 8px;
      color: var(--ion-color-danger);
      &:hover {
        cursor: pointer;
      }
    }
  }
}
</style>
