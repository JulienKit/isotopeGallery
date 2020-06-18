<template>
  <div id="app">
    <IsotopeGallery :filter-control="comp" fetch="FetchImages" />
  </div>
</template>

<script>
import IsotopeGallery from './components/IsotopeGallery.vue'
import IstpFilterControlBar from "./components/IstpFilterControlBar.vue";

export default {
  name: 'App',
  components: {
    IsotopeGallery,
    IstpFilterControlBar //eslint-disable-line
  },
  data() {
    return {
      comp: IstpFilterControlBar
    }
  }
} 
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 2rem;
}
</style>
