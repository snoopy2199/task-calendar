<template>
  <div id="app">
    <Board />
  </div>
</template>

<script>
import Board from './components/Board.vue';

export default {
  name: 'app',
  components: {
    Board,
  },
};
</script>

<style lang="scss">
body {
  margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
