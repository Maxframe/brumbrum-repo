<template>
  <div class="home">
    <h1>Homeee</h1>
    <Map></Map>
  </div>
</template>

<script>
// @ is an alias to /src
import Map from '@/components/Map.vue'

export default {
  name: 'Home',
  components: {
    Map
  }
}
</script>

<style>
body{
  margin: 0;
  padding: 0;
}
</style>
