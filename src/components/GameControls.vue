<template>
  <div class="game-controls">
    <button class="btn btn-primary" @click="$emit('startGame')" v-if="!gameStarted">
      🎮 開始遊戲
    </button>
    <button class="btn btn-secondary" @click="$emit('resetGame')">🔄 重新開始</button>
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
  emits: ['startGame', 'resetGame'],
}
</script>

<style scoped>
.game-controls {
  display: flex;
  justify-content: center;
  gap: clamp(12px, 3vw, 20px);
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.btn {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  padding: clamp(10px 16px, 3vw, 14px 24px);
  border-radius: 12px;
  font-size: clamp(0.85rem, 3vw, 1rem);
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow:
    0 4px 12px rgba(102, 126, 234, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  min-width: clamp(100px, 25vw, 130px);
  position: relative;
  overflow: hidden;
}

.btn::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
  transition: left 0.5s;
}

.btn:hover::before {
  left: 100%;
}

.btn:hover {
  transform: translateY(-2px);
  box-shadow:
    0 8px 20px rgba(102, 126, 234, 0.4),
    0 0 0 1px rgba(255, 255, 255, 0.2);
}

.btn:active {
  transform: translateY(0);
  box-shadow:
    0 2px 8px rgba(102, 126, 234, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.1);
}

.btn-primary {
  background: linear-gradient(135deg, #48bb78 0%, #38a169 100%);
  box-shadow:
    0 4px 12px rgba(72, 187, 120, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.1);
}

.btn-primary:hover {
  box-shadow:
    0 8px 20px rgba(72, 187, 120, 0.4),
    0 0 0 1px rgba(255, 255, 255, 0.2);
}

.btn-secondary {
  background: linear-gradient(135deg, #ed8936 0%, #dd6b20 100%);
  box-shadow:
    0 4px 12px rgba(237, 137, 54, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.1);
}

.btn-secondary:hover {
  box-shadow:
    0 8px 20px rgba(237, 137, 54, 0.4),
    0 0 0 1px rgba(255, 255, 255, 0.2);
}

/* 手機版優化 */
@media (max-width: 480px) {
  .game-controls {
    gap: 10px;
    margin-bottom: 16px;
    padding: 0 10px;
  }

  .btn {
    padding: 10px 14px;
    font-size: 0.85rem;
    min-width: 90px;
    border-radius: 10px;
  }
}

/* 平板版優化 */
@media (min-width: 481px) and (max-width: 768px) {
  .btn {
    padding: 12px 20px;
    font-size: 0.95rem;
  }
}

/* 桌面版優化 */
@media (min-width: 1024px) {
  .game-controls {
    gap: 20px;
    max-width: 300px;
    margin: 0 auto 20px auto;
  }

  .btn {
    padding: 14px 24px;
    font-size: 1rem;
    min-width: 130px;
  }
}

/* 觸控設備優化 */
@media (hover: none) and (pointer: coarse) {
  .btn {
    padding: 12px 20px;
    font-size: 1rem;
  }

  .btn:hover {
    transform: none;
  }

  .btn:active {
    transform: scale(0.95);
  }
}
</style>
