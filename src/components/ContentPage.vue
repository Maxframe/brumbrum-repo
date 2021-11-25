<template>
  <div class="bg-container" :id="number">
    <div class="content-container">
      <div id="nav">
        <div class="logo">
          <router-link to="/">
            <img src="../assets/media/logo.png" alt="Logo" />
            <div class="page-title">
              <span class="title-1">BRUM BRUM<br /></span>
              <span class="title-2">TOURS<br /></span>
              <span class="title-3">Glaubenberg</span>
            </div>
          </router-link>
        </div>

        <ul>
          <li><router-link to="/about">About</router-link></li>
        </ul>
      </div>
      <div class="media-container">
        <div class="contentPage">
          <!-- <video src="videos[0].fields.file.url" /> -->
          <li v-for="i in images" :key="i.fields.file.url">
            <img :src="i.fields.file.url" />
            <div class="text-box-wrapper-wrapper">
              <div class="text-box-wrapper">
                <div class="text-box-container">
                  <h1 class="text-box-title">{{ title }}</h1>
                  <p class="text-box">{{ i.fields.description }}</p>
                </div>
              </div>
            </div>
          </li>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "contentPage",
  props: {
    number: String,
    title: String,
    text: String,
    location: String,
    images: Array,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
  margin: 0;
  padding: 0;
}

.bg-container {
  transform-origin: top right;
  transform: rotate(15deg) translate(0, 0);
  position: fixed;

  /*--- bg-container Breite (zweiten Wert auskommentieren um Stop zu aktivieren) ---*/

  /* Breite für Fahrt*/
  width: 50vw;
  /* Breite für Stop*/
  width: 70vw;

  height: 200vh;
  background: #2d2d2d;
  overflow: hidden;
  z-index: 5;
  transition: ease-in-out 1s;
}

.content-container {
  position: relative;
  transform-origin: top right;
  transform: rotate(-15deg) translate(0);
  width: 100%;
  height: 100vh;
}

#nav {
  padding: 2rem;
  z-index: 10;
  position: relative;
}

#nav a {
  font-weight: bold;
  color: #fff;
  text-decoration: none;
}

#nav .logo a {
  position: absolute;
  display: flex;
}

#nav .logo img {
  width: auto;
  height: 10rem;
}

.page-title {
  display: inline;
  padding: 0.3rem 0 0 1rem;
}

.title-1 {
  font-family: teko, sans-serif;
  font-size: 2.4rem;
  font-weight: 300;
  letter-spacing: 0.15rem;
}

.title-2 {
  font-family: teko, sans-serif;
  font-size: 3.55rem;
  font-weight: 500;
  letter-spacing: 0.12rem;
  line-height: 2.5rem;
}

.title-3 {
  font-family: flood-std, sans-serif;
  font-size: 1.7rem;
  font-weight: 400;
  line-height: 2rem;
}

#nav ul {
  display: flex;
  flex-direction: row-reverse;
  list-style-type: none;
  margin: 0;
}

#nav ul li {
  padding: 0.3rem 2rem;
}

.media-container {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 100vh;
}

img {
  height: 100%;
  width: 100%;
  object-fit: cover;
}

video {
  height: 100%;
  width: 100%;
  object-fit: cover;
}
.text-box-wrapper-wrapper {
  position: absolute;
  bottom: 4rem;
  left: 2rem;
  right: 15rem;
  height: 8rem;
  overflow: hidden;
  z-index: 10;
}
.text-box-wrapper {
  transform-origin: top right;
  transform: rotate(15deg) translate(0, 0);
  position: absolute;
  width: 100%;
  height: 500%;
  background: #0000007c;
}

.content-container {
  transform-origin: top right;
  transform: rotate(-15deg) translate(0);
  width: 100%;
  height: 100vh;
}

.text-box-container {
  position: absolute;
  transform-origin: top right;
  transform: rotate(-15deg) translateY(0.8rem);
  width: 90%;
  height: 8rem;
}

h1.text-box-title {
  margin: 1.2rem 1rem 0 3rem;
  color: #f47525;
  vertical-align: middle;
}

p.text-box-text {
  margin: -0.3rem 1rem 1.2rem 3rem;
  color: white;
  vertical-align: middle;
}

.text-box::before {
  content: " ";
  position: absolute;
  width: 0.2rem;
  height: 6rem;
  background-color: white;
  left: 2rem;
}

.media-overlay {
  position: absolute;
  height: 36%;
  width: 100%;
  background-size: cover;
  background-position: center center;
  background-image: linear-gradient(to bottom, #1e1e1e 0%, #00000000 100%);
  z-index: 6;
}

.Map {
  z-index: 0;
}

#map {
  height: 100vh;
  position: fixed;
  right: 0;

  /*--- Map Breite (zweiten Wert auskommentieren um Stop zu aktivieren) ---*/

  /* Breite für Fahrt*/
  width: calc(60vw + 30rem);
  /* Breite für Stop*/
  width: calc(30vw + 20rem);

  transition: ease-in-out 1s;
}
</style>
