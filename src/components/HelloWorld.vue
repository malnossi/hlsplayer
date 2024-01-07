<template>
  <v-container class="pa-4">
    <v-row>
      <v-col cols="12" md="4" v-for="(item, i) in items" :key="i">
        <v-card>
          <v-responsive class="pa-4">
            <v-img :src="item.cover" contain :aspect-ratio="4 / 3">
            </v-img>
          </v-responsive>
          <v-card-text class="text-center align-self-end">
            <v-btn flat :icon="setIcon(item)" @click="playSong(item)"></v-btn>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-bottom-navigation v-show="song" height="150" class="pa-4">
      <v-col>
        <v-sheet>
          <div id="playercontainer"></div>
          <v-list>
            <v-list-item>
              <v-list-item-title>{{ song?.name }}</v-list-item-title>

              <v-list-item-subtitle>{{ song?.artist }}</v-list-item-subtitle>

              <template v-slot:prepend>
                <v-avatar :image="song?.cover" size="large" rounded="0"></v-avatar>
              </template>
            </v-list-item>
          </v-list>
        </v-sheet>
      </v-col>
    </v-bottom-navigation>
  </v-container>
</template>
<script>
import songs from "@/data/songs.json"
import Plyr from 'plyr'
export default {
  data: () => ({
    items: [],
    transparent: 'rgba(255, 255, 255, 0)',
    song: null,
    player: null,
    hls: null,
    audio: null,
    playing: false,
  }),
  computed: {
    display() {
      return this.$vuetify.display
    },
  },
  mounted() {
    this.items = songs
  },
  methods: {
    playSong(song) {
      if (this.player) {
        this.player.destroy()
        this.hls.destroy()
        document.getElementById("currentaudio").remove()
      }
      this.song = song
      this.audio = document.createElement("audio")
      this.audio.id = "currentaudio"
      if (Hls.isSupported()) {
        this.hls = new Hls()
        this.hls.loadSource(song.location)
        this.hls.attachMedia(this.audio)
      }
      document.getElementById("playercontainer").appendChild(this.audio)
      this.player = new Plyr(this.audio, {
        playsinline: true,
        controls: [
          'play', // Play/pause playback
          'progress', // The progress bar and scrubber for playback and buffering
          'current-time', // The current time of playback
          'duration', // The full duration of the media
          'mute', // Toggle mute
          'volume', // Volume control
        ]
      })
      this.player.togglePlay()

      this.player.on('play', () => {
        this.playing = true
      })

      this.player.on('pause', () => {
        this.playing = false
      })
    },
    setIcon(song) {
      if (!this.song) {
        return "mdi-play"
      }
      if (this.song.name == song.name) {
        return "mdi-pause"
      }
      return "mdi-play"
    }
  }
}
</script>
<style scoped>
.v-card {
  transition: opacity .4s ease-in-out;
}

.v-card:not(.on-hover) {
  opacity: 0.6;
}

.show-btns {
  color: rgba(255, 255, 255, 1) !important;
}

.image {
  -webkit-animation: spin 4s linear infinite;
  -moz-animation: spin 4s linear infinite;
  animation: spin 4s linear infinite;
}

@-moz-keyframes spin {
  100% {
    -moz-transform: rotate(360deg);
  }
}

@-webkit-keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
  }
}

@keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
</style>
