<template>
  <div id="app">
    <MemoryCardGame />
  </div>
</template>

<script>
import MemoryCardGame from './components/MemoryCardGame.vue'

export default {
  name: 'App',
  components: {
    MemoryCardGame,
  },
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Arial', sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
}

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px;
}
</style>
