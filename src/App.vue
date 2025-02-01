<template>
  <PhotoGallery msg="Hello Vue 3 in CodeSandbox!" />
</template>

<script>
import PhotoGalleryVue from "./components/PhotoGallery.vue";
export default {
  name: "App",
  components: {
    PhotoGallery: PhotoGalleryVue,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  font-size: 16px;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
