<template>
  <div id="app">
    <!-- <webglPanoramaCube /> -->
    <webglPanoramaEquirectangular></webglPanoramaEquirectangular>
  </div>
</template>

<script>
// import webglPanoramaCube from './components/webglPanoramaCube.vue'
import webglPanoramaEquirectangular from '@/components/webglPanoramaEquirectangular'

export default {
  name: 'App',
  components: {
    // webglPanoramaCube,
    webglPanoramaEquirectangular
  }
}
</script>

<style>
#app {
 padding: 0;
 margin: 0;
}
</style>
