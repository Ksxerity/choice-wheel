<template>
  <div id="app">
    <Wheel></Wheel>
  </div>
</template>

<script>
import Wheel from './components/wheel.vue';

export default {
  name: 'App',
  components: {
    Wheel,
  },
};
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
