<template>
<div class="wrapper">
<div id="nav">
    <div class="logo">
        <router-link to="/">
            <img src="../assets/media/logo_black.png" alt="Logo">
        <div class="page-title">
            <span class="title-1">BRUM BRUM<br></span>
            <span class="title-2">TOURS<br></span>
            <span class="title-3">Glaubenberg</span>
        </div>
        </router-link>
    </div>
    <ul>
        <li> <router-link to="/">Zurück</router-link></li>
    </ul>
</div>
  <div class="impressum">
    <img class="impressum-img" src="../assets/media/impressum.png" alt="Bild mit den vier Fahrern und ihren Töffs">
    <p class="impressum-names names1">Max</p>
    <p class="impressum-names names2">Nathalie</p>
    <p class="impressum-names names3">Daniel</p>
    <p class="impressum-names names4">Simon</p>
    
</div>
  <div class="section">
    <img class="brush-divider" src="../assets/media/brushstroke_divider.png" alt="">
    <div class="text-container">
      <div class="about-text-wrapper">
      <p class="about-text">

      Made by Digital Ideation Brum Brum Team at Studio Web2021
      </p>
      </div>

    </div>
  </div>
</div>


</template>

<style scoped>



.section {
  position: relative;
  margin-top: 34%;
}

#nav {
height: 10rem;
}

#nav a {
    color: #000;
}


.impressum-names {
  display: none;
  position: absolute;
  font-family: teko, sans-serif;
  color: #F47525;
  font-size: 2rem;
  text-transform: uppercase;
}


.impressum-img {
  position: absolute;
  top: 30%;
  width: 100%;
  height: auto;
  z-index: 5;
}

.brush-divider {
  display: block;
  top: 48%;
  width: 100%;
  height: auto;
  object-fit: contain;
}

.text-container {
  display: block;
  background-color: #000;
  width: 100%;
  top: 64%;
/*   height: 20rem; */
  text-align: center;
  padding-top: 10%;
  min-height: 18rem;;
}

.about-text-wrapper {
  margin: auto;
  max-width: 30rem;
  text-align: left;
  padding: 2rem 2rem 4rem 2rem;
  }

.about-text {
  color: #fff;
}
</style>
