<template>
  <Hospitals />
</template>

<script>
import Hospitals from './components/Hospitals.vue'

export default {
  name: 'App',
  components: {
    Hospitals
  }
}
</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */
body {
  background: #141626 !important;
  padding: 20px;
}
</style>
