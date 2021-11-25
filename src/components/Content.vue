<template>
  <div class="content">
    <h1>{{ number + " " + title }}</h1>
    <li v-for="i in images" :key="i.fields.file.url">
      <img
        :src="i.fields.file.url"
        :style="{ height: i.fields.file.details.image.height * 0.1 + 'px' }"
        :alt="i.fields.description"
      />
      <div id="imgText">{{ i.fields.description }}</div>
    </li>
  </div>
  <router-view />
</template>

<script>
export default {
  name: "content",
  props: {
    number: String,
    title: String,
    text: String,
    location: String,
    images: Array,
  },
};
</script>

<style>
#nav {
  padding: 30px;
}
#nav a {
  font-weight: bold;
  color: #2c3e50;
}
#nav a.router-link-exact-active {
  color: #42b983;
}
#imgText {
  font-size: 20px;
}
</style>
