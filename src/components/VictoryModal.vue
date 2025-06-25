<template>
  <div class="victory-modal" @click="handleBackdropClick">
    <div class="victory-content" @click.stop>
      <h2 class="victory-title">🎉 恭喜過關！</h2>
      <div class="victory-stats">
        <p>時間：{{ formatTime(finalTime) }}</p>
        <p>正確次數：{{ correctMatches }}</p>
        <p>錯誤次數：{{ wrongMatches }}</p>
        <p>正確率：{{ accuracy }}%</p>
      </div>
      <button class="btn btn-primary" @click="$emit('close')">繼續</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VictoryModal',
  props: {
    finalTime: {
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
  emits: ['close'],
  methods: {
    formatTime(seconds) {
      const m = String(Math.floor(seconds / 60)).padStart(2, '0')
      const s = String(seconds % 60).padStart(2, '0')
      return `${m}:${s}`
    },
    handleBackdropClick() {
      this.$emit('close')
    },
    handleEscKey(event) {
      if (event.key === 'Escape') {
        this.$emit('close')
      }
    },
  },
  mounted() {
    document.addEventListener('keydown', this.handleEscKey)
  },
  beforeUnmount() {
    document.removeEventListener('keydown', this.handleEscKey)
  },
}
</script>

<style scoped>
.victory-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease-out;
}

.victory-content {
  background: linear-gradient(135deg, #667eea, #764ba2);
  padding: 40px;
  border-radius: 20px;
  text-align: center;
  color: white;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.3);
  max-width: 500px;
  width: 90%;
  animation: slideIn 0.3s ease-out;
}

.victory-title {
  font-size: 2rem;
  margin-bottom: 20px;
}

.victory-stats {
  margin-bottom: 30px;
}

.victory-stats p {
  margin: 10px 0;
  font-size: 1.1rem;
}

.btn {
  background: linear-gradient(135deg, #00b894, #00a085);
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

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideIn {
  from {
    transform: translateY(-50px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>
