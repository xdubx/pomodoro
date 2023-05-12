<template>
  <input
    v-model="mediaUrl"
    placeholder="Streaming URL"
    class="transparent-button"
  />

  <iframe
    v-if="mediaUrl.length"
    ref="player"
    class="extendedMedia"
    :src="mediaUrlIframe"
    title="Music player"
    frameborder="0"
    sandbox="allow-scripts allow-same-origin allow-presentation"
    allowfullscreen
    allow="autoplay"
  >
  </iframe>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { PlayerType } from "../types/player-type.enum";

// TODO: keyboard controls
export default defineComponent({
  name: "MusicPlayer",

  props: { stopCondition: Boolean },

  data() {
    return {
      mediaUrl: "",
      mediaUrlIframe: "",
      currentPlayer: PlayerType.NONE,
    };
  },

  watch: {
    stopCondition(val) {
      if (localStorage.getItem("pauseMusic") === "false") {
        return;
      }
      const player = this.$refs.player as HTMLIFrameElement;
      if (player && player.contentWindow) {
        if (val) {
          this.stopMusic();
        } else {
          this.startMusic();
        }
      }
    },
    mediaUrl(val: string) {
      // detect url

      // youtube
      // https://www.youtube.com/watch?v=rxAokx0yfT8

      // single video
      if (this.youtube_validate(val)) {
        this.currentPlayer = PlayerType.YOUTUBE;
        if (!val.includes("list")) {
          const id = this.getId(val);
          this.mediaUrlIframe = `https://www.youtube.com/embed/${id}?autoplay=1&rel=0&enablejsapi=1&controls=0&rel=0`;
          return;
        } else {
          // playlist
          const list = this.getVideoSeries(val);
          this.mediaUrlIframe = `https://www.youtube.com/embed/?listType=playlist&list=${list}&autoplay=1&enablejsapi=1`;
          return;
        }
      }

      //TODO: implement spotify
      if (this.spotify_validate(val)) {
        this.getSpotifyId(val);
        const id = "";
        //this.mediaUrlIframe = `https://open.spotify.com/embed/album/${}`;
      }
      // detect spotify

      // https://open.spotify.com/embed/album/1DFixLWuPkv3KT3TnV35m3
      // if(false){
      //     const id = ""
      //     this.mediaUrlIframe = `https://open.spotify.com/embed/album/${}`;
      // }
      // https://open.spotify.com/embed/playlist/5a2OuIJ1kEttA8X3PaewlI?utm_source=oembed"

      // https://open.spotify.com/embed/track/39zWYYZStDgWi32sOU9AX4?si=BLFB8HxuQNC-OaHtwD_E4A

      //TODO: implement soundcloud
    },
  },
  methods: {
    // TODO: move this to utilities
    // TODO: shared links don't work
    youtube_validate(url: string) {
      var regExp =
        /^(?:https?:\/\/)?(?:www\.)?youtu\.?be(?:\.com)?\/?.*(?:watch|embed)?(?:.*v=|v\/|\/)([\w\-_]+)?$/;
      // add short url
      const urlArray = url.match(regExp);
      return urlArray && urlArray.length > 0;
    },
    spotify_validate(url: string) {
      var regExp = /^(spotify:|https:\/\/[a-z]+\.spotify\.com\/)/;
      const urlArray = url.match(regExp);
      return urlArray && urlArray.length > 0;
    },
    getId(url: string) {
      const regExp =
        /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|&v=)([^#&?]*).*/;
      const match = url.match(regExp);

      return match && match[2].length === 11 ? match[2] : null;
    },
    getVideoSeries(url: string) {
      var reg = new RegExp("[&?]list=([a-z0-9_]+)", "i");
      var match = reg.exec(url);

      if (match && match[1].length > 0) {
        return match[1];
      } else {
        return "nope";
      }
    },
    getSpotifyId(url: string) {
      const regex =
        /^(?:https:\/\/open\.spotify\.com|spotify)([\/:])user\1([^\/]+)\1playlist\1([a-z0-9]+)/gi; // eslint-disable-line
      const match = url.match(regex);
      console.log(match);
      if (match) {
        console.log("user id: " + match[2]);
        console.log("playlist id: " + match[3]);
      }
    },
    startMusic() {
      if (this.currentPlayer === PlayerType.YOUTUBE) {
        const player = this.$refs.player as HTMLIFrameElement;
        if (player && player.contentWindow !== null) {
          console.log("play");
          player.contentWindow.postMessage(
            JSON.stringify({ event: "command", func: "playVideo" }),
            "*"
          );
        }
      }
    },
    stopMusic() {
      if (this.currentPlayer === PlayerType.YOUTUBE) {
        const player = this.$refs.player as HTMLIFrameElement;
        if (player && player.contentWindow !== null) {
          player.contentWindow.postMessage(
            JSON.stringify({ event: "command", func: "pauseVideo" }),
            "*"
          );
        }
      }
    },
  },
});
</script>

<style scoped>
@import "../theme/global.scss";
.extendedMedia {
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
  height: 100%;
  width: 100%;
}
.inputUrl {
  border-radius: 20px;
}
</style>
