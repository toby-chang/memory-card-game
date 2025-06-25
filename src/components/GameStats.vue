<template>
  <div class="game-stats">
    <div class="stat-item">
      <span class="stat-value">{{ formatTime(gameTime) }}</span>
      <span class="stat-label">遊戲時間</span>
    </div>
    <div class="stat-item">
      <span class="stat-value">{{ correctMatches }}</span>
      <span class="stat-label">翻對次數</span>
    </div>
    <div class="stat-item">
      <span class="stat-value">{{ wrongMatches }}</span>
      <span class="stat-label">翻錯次數</span>
    </div>
    <div class="stat-item">
      <span class="stat-value">{{ accuracy }}%</span>
      <span class="stat-label">正確率</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameStats',
  props: {
    gameTime: {
      type: Number,
      default: 0,
    },
    correctMatches: {
      type: Number,
      default: 0,
    },
    wrongMatches: {
      type: Number,
      default: 0,
    },
    accuracy: {
      type: Number,
      default: 100,
    },
  },
  methods: {
    formatTime(seconds) {
      const m = String(Math.floor(seconds / 60)).padStart(2, '0')
      const s = String(seconds % 60).padStart(2, '0')
      return `${m}:${s}`
    },
  },
}
</script>

<style scoped>
.game-stats {
  display: flex;
  justify-content: center;
  gap: 30px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.stat-item {
  background: rgba(255, 255, 255, 0.2);
  padding: 15px 20px;
  border-radius: 15px;
  text-align: center;
  color: white;
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.3);
  min-width: 120px;
}

.stat-value {
  font-size: 1.8rem;
  font-weight: bold;
  display: block;
}

.stat-label {
  font-size: 0.9rem;
  opacity: 0.8;
}

@media (max-width: 768px) {
  .game-stats {
    gap: 15px;
  }

  .stat-item {
    min-width: 100px;
    padding: 10px 15px;
  }

  .stat-value {
    font-size: 1.4rem;
  }
}
</style>
