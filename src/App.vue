<template>
  <div id="app">
    <Game :size="600"></Game>
  </div>
</template>

<script>
import Game from './components/Game.vue';

export default {
  name: 'app',
  components: {
    Game,
  },
}
</script>

<style>

</style>
