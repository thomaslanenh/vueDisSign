<template>
  <audio :src="current" @timeupdate="time = $event.target.currentTime" ref="audio" @ended="nextTrack()" autoload="auto" preload="auto" autoplay></audio>
  <div class="parent">
      <div class="mainBlock" >
        <div>
          <transition name="fade" appear>
            <img :src="bgImg" :key="bgImg"/>
          </transition>
        </div>
        <transition name="fade">
        <div class="innerBlock fade-in-text" key="data">
          <h1 class="centerText">{{ride.name}}</h1>
          <p>{{data.description}}</p>
          <div class="rideTimes" v-if="ride.status === 'Operating'">
            <p class="centerText">Current Wait Time:</p>
            <h2 class="centerText" v-if="ride.waitTime != null">{{ride.waitTime}} minutes</h2>
            <h2 class="centerText" v-else>No Wait</h2>
          </div>
          <div v-else class="closeRide centerText">
            <p>Currently Closed</p>
          </div>
        </div>
        </transition>
      </div>

  </div>

</template>

<script>
export default {
  name: 'App',
  data(){
    let playlist = ['https://www.pythonanywhere.com/user/disneyquesting/files/home/disneyquesting/Pirates of the Caribbean - Queue Area Music Loop.mp4', 'https://www.pythonanywhere.com/user/disneyquesting/files/home/disneyquesting/Fantasyland.mp3','https://www.pythonanywhere.com/user/disneyquesting/files/home/disneyquesting/Adventureland.mp3','https://www.pythonanywhere.com/user/disneyquesting/files/home/disneyquesting/SpaceMountain.mp3','https://www.pythonanywhere.com/user/disneyquesting/files/home/disneyquesting/Frontierland.mp3','https://www.pythonanywhere.com/user/disneyquesting/files/home/disneyquesting/Tommorrowland.mp3','https://www.pythonanywhere.com/user/disneyquesting/files/home/disneyquesting/MainStreetUSA.mp3'];
    return {
      data: {},
      bgImg: {},
      ride: {},
      time: 0,
      playing: true,
      current: playlist[Math.floor(Math.random() * playlist.length)],
      playlist: playlist
    }
  },
  watch: {
    time(time){
      if (Math.abs(time - this.$refs.audio.currentTime) > 0.5){
        this.$refs.audio.currentTime = time
      }
    }
  },
  created(){
    this.getRides();
    this.timer = setInterval(this.getRides, 30000)
  },
  beforeMount() {
    this.getRides()
  },
  methods:{
    nextTrack: function() {
      if (this.playlist.length > 0) {
        this.current = this.playlist.shift();
        this.playing = true;
        this.play()
      }
      else {
        this.current = "";
        this.playing = false;
      }
    } ,
    play: function(){
      if (!this.playing){
        if (!this.current && this.playlist.length > 0){
          this.$refs.audio.pause();
          this.current = this.playlist.shift();
        }
        if (!this.current){
          this.playing = false
        }
        else {
          this.playing = true
        }
        this.$nextTick(()=>{
          if (this.playing){
            this.$refs.audio.play();
          }
        })
      } else {
        this.$refs.audio.pause()
        this.playing = false
      }
    },
    load: function() {
      this.$refs.audio.load()
    },
    pickRan: function(obj){
      var keys = Object.keys(obj);
      return obj[keys[ keys.length * Math.random() << 0]];
    },
    async getRides(){
      const mkwaitAPI = 'https://fast-forest-45299.herokuapp.com/https://api.themeparks.wiki/preview/parks/WaltDisneyWorldMagicKingdom/waittime';
      // const mkhoursAPI = 'https://api.themeparks.wiki/preview/parks/WaltDisneyWorldMagicKingdom/calendar';
      const rides = 'https://disneyquesting.pythonanywhere.com/attractions/?format=json&search=';

      const allRides = await fetch(mkwaitAPI).then(res=>res.json())
      const ranRide = await this.pickRan(allRides.filter(e=>e.meta.type === "ATTRACTION"))

      this.ride = ranRide;
      console.log(ranRide)
      let pickedRide = await fetch(rides + ranRide.name).then(res=>res.json());

      this.bgImg = pickedRide[0].image;

      this.data = pickedRide[0];


    },
    cancelAutoUpdate(){
      clearInterval(this.timer)
    }
  },
  components: {

  }
}
</script>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s ease;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

@font-face {
  font-family: "Columbus";
  src: local('COLUMBUS'), url(./fonts/COLUMBUS.TTF)format('truetype');
}

body{
  margin: 0px;
  background: #FCE97F;
}

.parent{
  height: 100vh;
}
h1{
  margin: 0px;
}

.centerText{
  text-align: center;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: white;
}

.mainBlock{
  height: 100%;
  background: black;
  background-position: center !important;
  background-size: cover !important;
  display: grid;
  grid-template-columns: 65% auto;
  background-repeat: repeat-x;
  animation: animatedBackground 120s linear infinite alternate;
  overflow: hidden;
}

.mainBlock img{
  height: 100vh;
  width: 100vw;
  object-fit: cover;
  object-position: center center;
}

.innerBlock{
  background: rgba(89, 90, 90, 0.53);
  padding:25px;
  align-self: center;
  margin-right: 25px;
  font-family: "Columbus", src;
  border-radius: 25px;
}

@keyframes animatedBackground {
  from {
    background-position: 50% 20%;
  }
  to {
    background-position: 100% 50%;
  }
}

@keyframes fadebackground {
  from {background-color: #FCE97F;}
  to {background-color: #2EB07B;}
}

.fade-in-text {
  animation: fadeIn linear 7s;
  -webkit-animation: fadeIn linear 7s;
  -moz-animation: fadeIn linear 7s;
  -o-animation: fadeIn linear 7s;
  -ms-animation: fadeIn linear 7s;
}

@keyframes fadeIn {
  0% {opacity:0;}
  100% {opacity:1;}
}

@-moz-keyframes fadeIn {
  0% {opacity:0;}
  100% {opacity:1;}
}

@-webkit-keyframes fadeIn {
  0% {opacity:0;}
  100% {opacity:1;}
}

@-o-keyframes fadeIn {
  0% {opacity:0;}
  100% {opacity:1;}
}

@-ms-keyframes fadeIn {
  0% {opacity:0;}
  100% {opacity:1;}
}
</style>
