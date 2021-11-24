<template>

<div class="bg-container">
    <div class="content-container">
        <div id="nav">
            <ul>
                <li><router-link to="/">Logo</router-link></li>
                <li> <router-link to="/media">Media</router-link></li>
                <li> <router-link to="/about">About</router-link></li>
            </ul>
        </div>
        <div class="media-container">
            <img src="../assets/testimage.jpg" alt="">
        </div>

    </div>

</div>

</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
    margin: 0;
    padding: 0;
}
    
#nav ul {
    display: flex;
    flex-direction: row;
    list-style-type: none;
}

#nav ul li {
    padding: 0.3em 2em;   
}

.bg-container {
    transform-origin: top right;
    transform: rotate(15deg) translate(0, 0);
    position: fixed;
    width: 55em;
    height: 130vh;
    background: #2d2d2d;;
    overflow: hidden;
    z-index: 1;
}

.content-container {
    transform-origin: top right;
    transform: rotate(-15deg) translate(0);
    width: 100%;
    height: 100vh;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #fff;
  text-decoration: none;
}

#nav a.router-link-exact-active {
  color: #f79e1a;
}

.Map {
    position: fixed;
    z-index: 0;
}

#map {
    height: 100vh;
    width: 100vw;
  } 
</style>
