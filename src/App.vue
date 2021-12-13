<template>
  <div id="app">
    <webglPanoramaCube />
  </div>
</template>

<script>
import webglPanoramaCube from './components/webglPanoramaCube.vue'

export default {
  name: 'App',
  components: {
    webglPanoramaCube
  }
}
</script>

<style>
#app {
 padding: 0;
 margin: 0;
}
</style>
