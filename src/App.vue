<template>
  <div id="app">
    <GrumpyCat/>
  </div>
</template>

<script>
import GrumpyCat from './components/GrumpyCat.vue'

export default {
  name: 'app',
  components: {
    GrumpyCat
  }
}
</script>

<style>
  body {
    background-color: black;
    font-family: Roboto;
  }
</style>
