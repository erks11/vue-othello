<template>
  <div id="app">
    <Othello></Othello>
  </div>
</template>

<script>
import Othello from "./components/Othello.vue";

export default {
  name: "app",
  components: {
    Othello
  }
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
