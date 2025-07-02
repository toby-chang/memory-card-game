<template>
  <div class="game-stats">
    <div class="stat-item">
      <span class="stat-value">{{ formatTime(gameTime) }}</span>
      <span class="stat-label">時間</span>
    </div>
    <div class="stat-item">
      <span class="stat-value">{{ correctMatches }}</span>
      <span class="stat-label">正確</span>
    </div>
    <div class="stat-item">
      <span class="stat-value">{{ wrongMatches }}</span>
      <span class="stat-label">錯誤</span>
    </div>
    <div class="stat-item">
      <span class="stat-value">{{ accuracy }}%</span>
      <span class="stat-label">準確率</span>
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
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: clamp(8px, 2vw, 16px);
  margin-bottom: 20px;
  padding: 0 5px;
}

.stat-item {
  background: linear-gradient(135deg, #f7fafc 0%, #edf2f7 100%);
  padding: clamp(10px, 3vw, 16px);
  border-radius: 12px;
  text-align: center;
  border: 1px solid #e2e8f0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease;
}

.stat-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.stat-value {
  font-size: clamp(1rem, 4vw, 1.4rem);
  font-weight: 700;
  display: block;
  color: #2d3748;
  margin-bottom: 2px;
}

.stat-label {
  font-size: clamp(0.7rem, 2.5vw, 0.85rem);
  color: #718096;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

/* 手機版優化 */
@media (max-width: 480px) {
  .game-stats {
    gap: 6px;
    margin-bottom: 16px;
  }

  .stat-item {
    padding: 8px 4px;
    border-radius: 8px;
  }

  .stat-value {
    font-size: 1rem;
  }

  .stat-label {
    font-size: 0.65rem;
  }
}

/* 平板版優化 */
@media (min-width: 481px) and (max-width: 768px) {
  .game-stats {
    gap: 12px;
  }

  .stat-item {
    padding: 12px 8px;
  }
}

/* 桌面版優化 */
@media (min-width: 1024px) {
  .game-stats {
    gap: 16px;
    max-width: 400px;
    margin: 0 auto 20px auto;
  }

  .stat-item {
    padding: 16px 12px;
  }
}
</style>
