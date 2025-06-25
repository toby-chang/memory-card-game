<template>
  <div class="game-controls">
    <button v-if="!gameStarted" class="btn btn-primary" @click="$emit('start')">開始遊戲</button>
    <button class="btn" @click="$emit('reset')">重新開始</button>
  </div>
</template>

<script>
export default {
  name: 'GameControls',
  props: {
    gameStarted: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['start', 'reset'],
}
</script>

<style scoped>
.game-controls {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 20px;
}

.btn {
  background: linear-gradient(135deg, #fd79a8, #e84393);
  color: white;
  border: none;
  padding: 15px 30px;
  border-radius: 25px;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.3);
}

.btn-primary {
  background: linear-gradient(135deg, #00b894, #00a085);
}
</style>
