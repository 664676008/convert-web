<template>
  <div id="app">
    <Convert/>
  </div>
</template>

<script>
import Convert from './components/convert.vue'

export default {
  name: 'App',
  components: {
    Convert,
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
  display: flex;
  justify-content: center;
  align-items: center;
}
body {
  margin: 0;
  overflow: hidden;
}
</style>
