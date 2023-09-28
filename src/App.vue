<template>
  <MyUploader />
</template>

<script>
import MyUploader from './components/MyUploader.vue'

export default {
  name: 'App',
  components: {
    MyUploader,
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  text-align: center;
}
</style>
