<template>
  <div class="home">
    <Map></Map>

    <ul id="array-rendering">
      <li v-for="c in contents" :key="c.fields.nummer">
        <ContentPage
          class="contentPage"
          id="c.fields.number"
          :number="c.fields.nummer"
          :title="c.fields.titel"
          :location="c.fields.location"
          :images="c.fields.bilder"
          :videos="c.fields.videos1"
          :goProVideos="c.fields.goProVideos"
        />
      </li>
    </ul>
  </div>
</template>

<script>
// @ is an alias to /src
import Map from "@/components/Map.vue";
// import Testlayout from "@/components/Testlayout.vue";
import ContentPage from "@/components/ContentPage.vue";
import { createClient } from "contentful";

export default {
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
.contentPage {
  /* display: none; */
}
</style>
