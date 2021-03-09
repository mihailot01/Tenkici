<template>
  <div id="app">
    <div id="tenkici">
        <h1 style="padding: 0.5rem;margin-bottom: 0;">Tenkići</h1>
        <Game ref="game"/>
    </div>
  </div>
</template>

<script>
import Game from './components/Game.vue'

export default {
  name: 'App',
  components: {
    Game
  },
  methods:{
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
  background-color: whitesmoke;
  max-width: 1200px;
  margin: auto;
}

body{
    background-color: gainsboro;
}
</style>
