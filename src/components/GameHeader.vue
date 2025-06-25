<template>
  <h1 class="game-title">🎴 記憶卡牌遊戲</h1>
</template>

<script>
export default {
  name: 'GameHeader',
}
</script>

<style scoped>
.game-title {
  text-align: center;
  color: white;
  font-size: 2.5rem;
  margin-bottom: 30px;
  text-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

@media (max-width: 768px) {
  .game-title {
    font-size: 2rem;
  }
}
</style>
