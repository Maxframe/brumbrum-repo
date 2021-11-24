<template>

<div class="wrapper">

<div class="bg-container">
    <div class="content-container">
        <div id="nav">
            <div class="logo"><router-link to="/">
                <img src="../assets/media/logo.png" alt="Logo">
            </router-link></div>
            <ul>
                <li> <router-link to="/about">About</router-link></li>
            </ul>
        </div>
        <div class="media-container">
            <div class="media-overlay"></div>
            <video autoplay muted loop id="myVideo">
                <source src="../assets/media/testvideo.mp4" type="video/mp4">
            </video>
            <img src="../assets/media/testimage.jpg" alt="Es Töffle">
        </div>

        <div class="text-box-wrapper-wrapper">
            <div class="text-box-wrapper">
                <div class="text-box-container">
                    <p class="text-box">Lorem ipsum dolor sit amet consectetur adipisicing elit. 
                        Consectetur eveniet accusamus incidunt at provident id explicabo odio iste tenetur eos.</p>
                </div>
            </div>
        </div>

    </div>

</div>
</div>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
    margin: 0;
    padding: 0;
}

.bg-container {
    transform-origin: top right;
    transform: rotate(15deg) translate(0, 0);
    position: absolute;

/*--- bg-container Breite (zweiten Wert auskommentieren um Stop zu aktivieren) ---*/
    
    /* Breite für Fahrt*/
    width: 40vw;
    /* Breite für Stop*/
    width: 70vw;
    
    height: 200vh;
    background: #2d2d2d;;
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
    padding: 30px;
    z-index: 10;
    position: relative;
}

#nav a {
    font-weight: bold;
    color: #fff;
    text-decoration: none;
}

#nav a.router-link-exact-active {
    color: #f79e1a;
}

#nav .logo {
position: absolute;
width: auto;
height: 160px;
}

#nav ul {
    display: flex;
    flex-direction: row-reverse;
    list-style-type: none;
    margin: 0;
}

#nav ul li {
    padding: 0.3em 2em;   
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

    top: 60vh;
    left: 30vw;
    width: 30vw;
    height: 10vh;

    overflow: hidden;
    z-index: 10;
}
.text-box-wrapper {
    transform-origin: top right;
    transform: rotate(15deg) translate(0, 0);
    position: absolute;
    width: 100%;
    height: 300%;
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
    transform: rotate(-15deg) translate(0);
    width: 90%;
    height: 100%;

}

p.text-box {
    margin: 0;
    padding: 0;
    color: white;
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
    float: right;
    height: 100vh;

    /*--- Map Breite (zweiten Wert auskommentieren um Stop zu aktivieren) ---*/
    
    /* Breite für Fahrt*/
    width: calc(60vw + 30em);
    /* Breite für Stop*/
    width: calc(30vw + 20em);

    transition: ease-in-out 1s;
  } 
</style>
