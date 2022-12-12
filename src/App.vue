<template>
  <div id="app">
    <h1>Performance</h1>

    <div class="roomName">
      <input :value="roomName" @input="event => roomName = event.target.value" />
    </div>

    <div>
      <button @click="connect">Connect</button>
    </div>

    <video v-for="track in videoTracks" :key="`track-${track.getId()}`" :ref="track.getId()" autoplay >
    </video>

    <audio v-for="track in audioTracks" :key="`track-${track.getId()}`" :ref="track.getId()" autoplay >
    </audio>

  </div>
</template>

<script>
import { connect, createAndJoinRoom, createTracksAndAddToRoom } from './utils/jitsiUtils';
import JitsiMeetJS from '@lyno/lib-jitsi-meet';

export default {
  name: 'App',
  
  setup() {

    return {}
  },

  data() {
    return {
      roomName: '',
      videoTracks: [],
      audioTracks: []
    }
  },

  methods: {
    addTrack(track) {
      if (track.getType() === 'video') {
        this.videoTracks.push(track);
      } else if (track.getType() === 'audio') {
        this.audioTracks.push(track);
      }
      this.$nextTick().then(() => {
        track.attach(this.$refs[track.getId()][0]);
      })
    },

    connect() {
      console.log(this.roomName)
      connect(this.roomName).then(connection => {
        return createAndJoinRoom(connection, this.roomName);
      })
      .then(room => {
        room.on(JitsiMeetJS.events.conference.TRACK_ADDED, track => this.addTrack(track));
        createTracksAndAddToRoom(room);
      })
      .catch(error => console.error(error));
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: block;
  margin-top: 60px;
}

.roomName {
  margin-bottom: 20px;
}
input {
  width: 300px;
  height: 30px;
  font-size: 20px;
}
button {
  width: 100px;
  height: 30px;
}

</style>
