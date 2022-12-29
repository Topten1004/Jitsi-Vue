<template>
  <div id="app">
      <div class="connect-form">
          <div class="title">Konferenz beitreten</div>
          <div class="sub-title"> Test </div>

          <md-field>
            <md-input
              :value="roomName"
              @input="event => roomName = event.target.value"
              class="room-name"
              placeholder="Enter your name"
            >
            </md-input>
          </md-field>

          <div class="prejoin-dropdown">
            <div class="join-meeting">
              <div class="join-form">
                Join meeting
              </div>
              <div>
                <img src="./assets/svg/arrowup.svg">
              </div>
            </div>
            <div></div>
          </div>
            <!-- <select name="country" id="country" placeholder="Join meeting">
              <option value="meeting" hidden>Join meeting</option>
              <option value="audio">Join without audio</option>
            </select> -->

          <div class="menu-item">
              <div class="icons">
                <img src="./assets/svg/mute.svg">
                <md-tooltip md-direction="top">Mute / Unmute</md-tooltip>
              </div>

              <div class="icons">
                <img src="./assets/svg/video.svg">
                <md-tooltip md-direction="top">Start / Stop camera</md-tooltip>
              </div>

              <div class="icons">
                <img src="./assets/svg/person.svg">
                <md-tooltip md-direction="top">Invite people</md-tooltip>
              </div>
            
              <div class="icons">
                <img src="./assets/svg/picture.svg">
                <md-tooltip md-direction="top">Select background</md-tooltip>
              </div>

              <div class="icons">
                <img src="./assets/svg/setting.svg">
                <md-tooltip md-direction="top">Setting</md-tooltip>
              </div>
          </div>

          <div class="warning">
              <img src="./assets/svg/warning.svg">
            <div class="warning-text">
              You need to enable microphone and camera access
            </div>
          </div>
      </div>
      <div class="video-form">
        <img src="./assets/avatar.png" class="avatar">
        <!-- <video v-for="track in videoTracks" :key="`track-${track.getId()}`" :ref="track.getId()" autoplay >
        </video>

        <audio v-for="track in audioTracks" :key="`track-${track.getId()}`" :ref="track.getId()" autoplay >
        </audio> -->
      </div>
  </div>
</template>

<script>
import { connect, createAndJoinRoom, createTracksAndAddToRoom } from './utils/jitsiUtils';
import JitsiMeetJS from '@lyno/lib-jitsi-meet';

export default {
  name: 'App',

  setup() {

    return {
    }
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
  display: flex;
  min-height: 100vh;
}

option {
  font-size: 14px;
}

.md-layout {
  width: 100%;
  margin-left: 0px !important;
  margin-right: 0px !important;
}
.title {
  font-size: 30px;
}
.sub-title {
  font-size: 16px;
}
.connect-form {
  min-width: 396px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #292929;
  color: white;
  gap: 20px;
  padding: 0px 30px;
}
.md-field {
  padding-top: 0px !important;
  min-height: unset !important;
  margin: 0px;
}
.md-input {
  height: 40px !important;
}
.room-name {
  text-align: center;
  background: white !important;
  border-radius: 4px;
}
.prejoin-dropdown {
  background-color: rgb(36, 111, 229);
  width: 100%;
  height: 40px;
  border-radius: 4px;
  text-align: center;
  font-weight: bold;
  color: white;
  cursor: pointer;
}
.prejoin-dropdown:hover {
  background-color: rgb(70, 135, 237);
}
.join-meeting {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  height: 100%;
  padding: 0px 10px;
}
.join-form {
  width: 100%;
  text-align: center;
}
.menu-item {
  display: flex;
  width: 100%;
  justify-content: space-around;
}
.icons {
  width: 48px;
  height: 48px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 4px;
}
.icons:hover {
  background: rgba(255,255,255,.2);
  cursor: pointer;
}
.warning {
  display: flex;
  background-color: rgb(248, 174, 26);
  border-radius: 6px;
  text-align: left;
  padding: 12px 16px;
}
.warning-text {
  color: black;
  margin-left: 16px;
}
.video-form {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #040404;
  width: 100%;
}
.avatar {
  background-color: grey;
  border-radius: 50%;
}

</style>
