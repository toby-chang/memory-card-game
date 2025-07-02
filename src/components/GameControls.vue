<template>
  <div class="game-controls">
    <div class="mode-selector">
      <label for="game-mode" class="mode-label">遊戲模式：</label>
      <select
        id="game-mode"
        :value="gameMode"
        @change="$emit('change-mode', $event.target.value)"
        class="mode-select"
      >
        <option value="2x2">2×2 (4張 - 測試)</option>
        <option value="4x3">4×3 (12張 - 簡單)</option>
        <option value="4x4">4×4 (16張 - 困難)</option>
      </select>
    </div>

    <button @click="$emit('restart')" class="restart-btn">🔄 重新開始</button>
  </div>
</template>

<script>
export default {
  name: 'GameControls',
  props: {
    gameMode: {
      type: String,
      required: true,
    },
  },
  emits: ['change-mode', 'restart'],
}
</script>

<style scoped>
.game-controls {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  margin: 20px 0;
}

.mode-selector {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.mode-label {
  color: white;
  font-size: 1rem;
  font-weight: 500;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}

.mode-select {
  background: rgba(255, 255, 255, 0.95);
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-radius: 12px;
  padding: 10px 15px;
  font-size: 1rem;
  color: #374151;
  cursor: pointer;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  min-width: 200px;
}

.mode-select:hover {
  background: rgba(255, 255, 255, 1);
  border-color: #667eea;
  transform: translateY(-1px);
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.15);
}

.mode-select:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.3);
}

.restart-btn {
  background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);
  color: white;
  border: none;
  border-radius: 12px;
  padding: 12px 24px;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(245, 158, 11, 0.3);
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

.restart-btn:hover {
  background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
  transform: translateY(-2px);
  box-shadow: 0 6px 18px rgba(245, 158, 11, 0.4);
}

.restart-btn:active {
  transform: translateY(0);
}

/* 手機優化 */
@media (max-width: 768px) {
  .game-controls {
    gap: 12px;
    margin: 15px 0;
  }

  .mode-select {
    min-width: 180px;
    padding: 8px 12px;
    font-size: 0.9rem;
  }

  .restart-btn {
    padding: 10px 20px;
    font-size: 0.9rem;
  }
}

/* 平板優化 */
@media (min-width: 769px) and (max-width: 1024px) {
  .game-controls {
    flex-direction: row;
    justify-content: center;
    align-items: center;
    gap: 20px;
  }

  .mode-selector {
    flex-direction: row;
    gap: 10px;
  }
}

/* 桌面優化 */
@media (min-width: 1025px) {
  .game-controls {
    flex-direction: row;
    justify-content: center;
    align-items: center;
    gap: 30px;
  }

  .mode-selector {
    flex-direction: row;
    gap: 12px;
  }
}
</style>
