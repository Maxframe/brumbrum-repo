<template>
  <div class="home">
    <Testlayout></Testlayout>
    <!-- <ContentPage></ContentPage> -->
    <Map></Map>
  </div>
</template>

<script>
// @ is an alias to /src
import Map from "@/components/Map.vue";
import Testlayout from "@/components/Testlayout.vue";
//import ContentPage from "@/components/ContentPage.vue";

export default {
  name: "Home",
  components: {
    Map,
    Testlayout,
  },
};
</script>

<style>
.home {
  position: relative;
  width: 100%;
  height:30000vh;
}

</style>
