<template>
  <div id="app">
    <Dashboard msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Dashboard from './components/Dashboard.vue'
import "normalize.css"

export default {
  name: 'app',
  components: {
    Dashboard
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
