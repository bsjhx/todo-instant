<template>
  <img alt="Vue logo" src="./assets/icon.svg" viewBox="0 0 60 55" width="250" height="250">
  <start msg="Welcome to Your Vue.js App"/>
</template>

<script>
import start from './components/Main.vue'

export default {
  name: 'App',
  components: {
    start
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
  margin-top: 60px;
}
</style>
