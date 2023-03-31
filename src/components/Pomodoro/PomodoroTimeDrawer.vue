<template>
  <img :src="image" alt="Timer" class="img" />
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "PomodoroTimeDrawer",

  props: {
    name: String,
    paused: Boolean,
    total: Number,
    elapsed: Number,
  },

  data() {
    return {
      currentRound: null,
      image: "",
    };
  },

  // TODO: update this calc
  mounted() {
    this.image = this.createTray(this.paused, this.total!, this.elapsed!);
  },
  watch: {
    elapsed(val) {
      this.image = this.createTray(this.paused, this.total!, val!);
    },
    paused(val) {
      this.image = this.createTray(val, this.total!, this.elapsed!);
    },
    total(val) {
      this.image = this.createTray(this.paused, val, this.elapsed!);
    },
  },
  methods: {
    // TODO: update size by param
    createTray(paused: boolean, total: number, elapsed: number) {
      const arcRadiusRatio = 0.9;
      const size = 300;
      const bgColor = "#2F384B"; // export as cont
      const fgColor = "#05EB8B";
      const outerRadius = size / 2;
      const arcLineWidthRatio = 0.1;

      const fullCircle = 2 * Math.PI;
      const startAngle = -Math.PI / 2;
      const innerRadius = outerRadius * arcRadiusRatio;

      const remainingTime = 1 - elapsed / total;
      const endAngle = remainingTime * fullCircle + startAngle;

      const center = outerRadius;
      const lineWidth = outerRadius * arcLineWidthRatio;
      const pauseStrokesHalfDistance = innerRadius / 1.7;
      const canvas = document.createElement("canvas");
      canvas.height = size;
      canvas.width = size;
      const ctx = canvas.getContext("2d");

      if (ctx !== null) {
        ctx.fillStyle = bgColor;
        ctx.strokeStyle = fgColor;
        ctx.lineWidth = lineWidth;

        ctx.beginPath();
        ctx.arc(center, center, outerRadius, 0, fullCircle, false);
        ctx.fill();

        if (paused) {
          ctx.beginPath();
          ctx.lineWidth = lineWidth + 2;
          ctx.moveTo(center - pauseStrokesHalfDistance, center - innerRadius);
          ctx.lineTo(center - pauseStrokesHalfDistance, center + innerRadius);
          ctx.moveTo(center + pauseStrokesHalfDistance, center - innerRadius);
          ctx.lineTo(center + pauseStrokesHalfDistance, center + innerRadius);
          ctx.stroke();
        } else {
          ctx.beginPath();

          ctx.arc(center, center, innerRadius, startAngle, endAngle, false);
          ctx.stroke();
        }

        const dataUrl = canvas.toDataURL("image/png");
        return dataUrl;
      }
      return "";
    },
  },
});
</script>

<style scoped>
.img {
  height: 100%;
  width: 100%;
}
</style>
