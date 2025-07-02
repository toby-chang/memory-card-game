<template>
  <div class="modal-overlay" @click="$emit('close')">
    <div class="modal-content" @click.stop>
      <div class="modal-header">
        <h2 class="modal-title">🎉 恭喜過關！</h2>
      </div>

      <div class="modal-body">
        <div class="stats-grid">
          <div class="stat-item">
            <div class="stat-label">時間</div>
            <div class="stat-value">{{ formattedTime }}</div>
          </div>

          <div class="stat-item">
            <div class="stat-label">正確次數</div>
            <div class="stat-value">{{ correctMatches }}</div>
          </div>

          <div class="stat-item">
            <div class="stat-label">錯誤次數</div>
            <div class="stat-value">{{ wrongMatches }}</div>
          </div>

          <div class="stat-item">
            <div class="stat-label">正確率</div>
            <div class="stat-value">{{ accuracy }}%</div>
          </div>
        </div>
      </div>

      <div class="modal-footer">
        <button class="continue-btn" @click="$emit('restart')">🔄 繼續挑戰</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VictoryModal',
  props: {
    gameTime: {
      type: Number,
      required: true,
    },
    correctMatches: {
      type: Number,
      required: true,
    },
    wrongMatches: {
      type: Number,
      required: true,
    },
    accuracy: {
      type: Number,
      required: true,
    },
  },
  emits: ['close', 'restart'],
  computed: {
    formattedTime() {
      if (isNaN(this.gameTime) || this.gameTime < 0) {
        return '00:00'
      }

      const minutes = Math.floor(this.gameTime / 60)
      const seconds = this.gameTime % 60
      return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
    },
  },
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  backdrop-filter: blur(5px);
}

.modal-content {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 20px;
  padding: 30px;
  max-width: 400px;
  width: 90%;
  color: white;
  text-align: center;
  box-shadow:
    0 25px 50px rgba(0, 0, 0, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  animation: modalSlideIn 0.3s ease-out;
}

@keyframes modalSlideIn {
  from {
    opacity: 0;
    transform: translateY(-20px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.modal-header {
  margin-bottom: 25px;
}

.modal-title {
  font-size: 1.8rem;
  margin: 0;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.modal-body {
  margin-bottom: 25px;
}

.stats-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
  margin: 20px 0;
}

.stat-item {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 15px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.stat-label {
  font-size: 0.9rem;
  opacity: 0.8;
  margin-bottom: 5px;
}

.stat-value {
  font-size: 1.5rem;
  font-weight: bold;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

.modal-footer {
  display: flex;
  justify-content: center;
  gap: 15px;
}

.continue-btn {
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  color: white;
  border: none;
  border-radius: 12px;
  padding: 12px 24px;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
}

.continue-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 18px rgba(16, 185, 129, 0.4);
}

.continue-btn:active {
  transform: translateY(0);
}

/* 手機優化 */
@media (max-width: 480px) {
  .modal-content {
    margin: 20px;
    padding: 25px;
  }

  .modal-title {
    font-size: 1.5rem;
  }

  .stats-grid {
    gap: 12px;
  }

  .stat-item {
    padding: 12px;
  }

  .stat-value {
    font-size: 1.3rem;
  }
}
</style>
