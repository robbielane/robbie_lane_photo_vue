<template>
  <div id="app">

    <div class="logo-container">
      <img id='logo' src="http://photography.robbielane.me/assets/logo1-8045567a41701f07e93cb76e8f82aa4475d6d5f897f91df13d20ca3059beb2d1.png">
      <h2>A <a href='https://vuejs.org/'>Vue.js</a> application that features <a href='http://photography.robbielane.me'>Robbie Lane Photography</a></h2>
    </div>

    <div id="gallery" v-if='showGal'>
      <span @click='clearGal()'>CLOSE</span>
      <h1>{{ currentGal.name }}</h1>
      <h3>{{ currentGal.location }}</h3>
      <ul id='gal-pics'>
        <li v-for='pic in currentGal.all_pics'>
          <img :src="pic">
        </li>
      </ul>
    </div>

    <div id='show-pic' v-if='showPic'>
      <span @click='clearShowPic()'>CLOSE</span>
      <h2>{{ currentPic.name }}</h2>
      <img :src="currentPic.image_url" />
      <p>{{ currentPic.description }}</p>
    </div>

    <div class="container">
      <h2>Most Recent Photos</h2>
      <ul id='picss'>
        <li v-for='pic in pictures' class='pic' @click='togglePic(pic)'>

          <img :src='pic.image_url' />
          <figure v-if='picDesc(pic)'>{{ pic.description }}</figure>
        </li>
      </ul>
    </div>

    <div class="container">
      <h2>Galleries</h2>
      <ul id='picss'>
        <li v-for='gal in galleries' class='gal' @click="showGallery(gal)">
          <h5>{{ gal.name }}</h5>
          <figure>
            {{ gal.location }}
          </figure>
          <img :src='gal.cover' />
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'app',
  data: () => ({
    pictures: [],
    galleries: [],
    showGal: false,
    showPic: false,
    currentGal: null,
    currentPic: null,
    showPicDescs: []
  }),
  beforeMount() {
    axios.get('http://photography.robbielane.me/api/v1/recent.json').then( (response) => {
      console.log(response.data);
      this.pictures = response.data
    });

    axios.get('http://photography.robbielane.me/api/v1/galleries.json').then( (response) => {
      console.log(response.data);
      this.galleries = response.data
    });
  },
  created: function() {
    this.checkForKeyEvent();
  },
  methods: {
    showGallery: function (gal) {
      axios.get(`http://photography.robbielane.me/api/v1/galleries/${gal.id}.json`).then( (response) => {
        console.log(response.data);
        this.currentGal = response.data;
        this.showGal = true;
      });
    },
    clearGal: function() {
      this.currentGal = null;
      this.showGal = false;
    },
    clearShowPic: function() {
      this.currentPic = null;
      this.showPic = false;
    },
    picDesc: function(pic) {
      return this.showPicDescs.includes(pic.id);
    },
    togglePic: function(pic) {
      this.currentPic = pic;
      this.showPic = true;
    },
    clearAll: function() {
      if (this.showGal || this.showPic) {
        this.showGal = false;
        this.showPic = false;
      }
    },
    checkForKeyEvent: function() {
      window.addEventListener('keyup', (e) => {
        if (e.which === 27) {
          this.clearAll();
        }
      })
    }
  }
}
</script>

<style>
body {
  margin: 0;
  width: 100vw;
  height: 100%;
  background-color: #e4e4e4;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#app .logo-container{
  background: linear-gradient(#999, #e4e4e4);
  margin-bottom: 20px;
  border-bottom: 1px dotted #fefefe;
}

#app .logo-container h2 a:hover {
  display: inline-block;
  filter: drop-shadow(1px 1px 1px #aaa);
  cursor: pointer !important;
}

#app #logo {
  filter: drop-shadow(5px 5px 3px #333);
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.container {
  border: 3px solid #666;
  margin: 0 40px 80px 40px;
  background-color: #eee;
}

.container h2 {
  text-align: center;
  font-weight: bolder;
  font-variant: small-caps;
  font-size: 26px;
  letter-spacing: 3px;
  color: #f06d06;
  padding-bottom: 8px;
  border-bottom: 2px solid #ccc;
}

.pic {
  width: 14%;
  box-sizing: border-box;
  display: inline-table;
  vertical-align: middle;
  filter: grayscale(0.6);
}

.pic:hover {
  cursor: pointer;
  filter: grayscale(0);
}

.pic img {
  width: 100%;
}

.pic h5 {
  font-size: 18px;
  font-weight: 800;
  margin: 10px 0;
}

.gal {
  box-sizing: border-box;
  padding:12px;
  width: 48%;
  display: inline-table;
  vertical-align: middle;
  border: 1px solid #aaa;
  margin: 25px 1%;
}

.gal:hover {
  background-color: #ccc;
  cursor: pointer;
}

.gal img {
  width: 100%;
}

.gal img:hover {
  filter: grayscale(0.4);
}

.gal h5 {
  font-size: 18px;
  font-weight: 800;
  margin-bottom: 0;
}

.gal figure {
  font-size: 12px;
  margin: 0 0 6px;
}

#gallery {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #000;
  color: #aaa;
  overflow: scroll;
  z-index: 100;
}

#gallery span {
  position: fixed;
  top: 24px;
  right: 24px;
  font-size: 14px;
  font-weight: bold;
  color: red;
  background-color: #f5f5f5;
  padding: 10px 20px;
  border: 2px solid red;
  border-radius: 5px;
  cursor: pointer;
}

#gallery span:hover {
  background-color: #999;
}

#gallery h1 {
  margin-bottom: 0;
}

#gallery h3 {
  margin-top: 0;
}

#gallery img {
  width: 100%;
  margin-bottom: 10px;
}

#show-pic {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #000;
  color: #aaa;
  overflow: scroll;
  z-index: 100;
}

#show-pic span {
  position: fixed;
  top: 24px;
  right: 24px;
  font-size: 14px;
  font-weight: bold;
  color: red;
  background-color: #f5f5f5;
  padding: 10px 20px;
  border: 2px solid red;
  border-radius: 5px;
  cursor: pointer;
}


</style>
