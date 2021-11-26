<template>
  <div class="home">
    <Map @changeChapter="changeChapter($event)" @changeImage="changeImage($event)"></Map>

    <div v-for="c in contents" :key="c.fields.nummer">
      <ContentPage
        class="contentPageWrapper"
        :number="c.fields.nummer"
        :title="c.fields.titel"
        :location="c.fields.location"
        :images="c.fields.bilder"
        :videos="c.fields.videos1"
        :goProVideos="c.fields.goProVideos"
      />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Map from "@/components/Map.vue";
// import Testlayout from "@/components/Testlayout.vue";
import ContentPage from "@/components/ContentPage.vue";
import { createClient } from "contentful";
export default {
  methods: {
    changeChapter(chapterIdx) {
      let chapterNummer = chapterIdx;
      let allChapters = document.getElementsByClassName("contentPageWrapper");
      for (let idx = 0; idx < allChapters.length; idx++){
        allChapters[idx].style.display = "none";
      }
      let activeChapter = document.getElementById(chapterNummer);
      activeChapter.style.display = "block";
    },
    changeImage(data){
      console.log("entered changeimage in Home")
      let activeChapter = document.getElementById(data.chapterIndex);
      let allMedias = activeChapter.getElementsByClassName("media-container");
      for (let idx = 0; idx < allMedias.length; idx++){
        allMedias[idx].style.display = "none";
      }
      let activeMedia = document.getElementById("section"+data.chapterIndex+"bild"+data.mediaIndex);
      activeMedia.style.display = "block";
      console.log(activeMedia);
    }
  },
  name: "Home",
  components: {
    Map,
    ContentPage,
  },
  data: function () {
    return {
      contents: [],
    };
  },
  created: function () {
    let client = createClient({
      space: "jfbiriazkehh",
      accessToken: "PJ2rc9wfcHt-OqFmTWgRF5usmXx7_8u3qAJ2cbWDdbI",
    });
    client
      .getEntries({
        content_type: "brumbrumRoute",
      })
      .then((entries) => {
        console.log(entries);
        this.contents = entries.items;
      });
  },
};
</script>

<style>
.home {
  position: relative;
  width: 100%;
  height: 30000vh;
}
.contentPageWrapper {
  display: none;
}

.mediaContainer{
  display: none;
}

.storymarker{
  background-image: url(../assets/media/marker.png);
  background-size: cover;
  width:3rem;
  height: 3rem;
}

.motomarker{
  background-image: url(../assets/media/LogoMotocycle.png);
  background-size: cover;
  width:3rem;
  height: 5rem;
}
</style>
