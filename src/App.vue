<template>
  <div id="app">
    <Tenkici/>
  </div>
</template>

<script>
import Tenkici from './components/Tenkici.vue'

export default {
  name: 'App',
  components: {
    Tenkici
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
  /*margin-top: 60px;*/
}
</style>
