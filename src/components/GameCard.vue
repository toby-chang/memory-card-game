<template>
  <div
    class="card"
    :class="{ flipped: card.flipped || card.matched, matched: card.matched }"
    @click="handleClick"
  >
    <div class="card-inner">
      <div class="card-face card-back">?</div>
      <div class="card-face card-front">{{ card.emoji }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameCard',
  props: {
    card: {
      type: Object,
      required: true,
    },
    index: {
      type: Number,
      required: true,
    },
  },
  emits: ['flip'],
  methods: {
    handleClick() {
      this.$emit('flip')
    },
  },
}
</script>

<style scoped>
.card {
  width: 80px;
  height: 80px;
  position: relative;
  perspective: 1000px;
  cursor: pointer;
}

.card-inner {
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.3s ease;
  position: relative;
}

.card.flipped .card-inner {
  transform: rotateY(180deg);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
  font-weight: bold;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
  border: 2px solid rgba(255, 255, 255, 0.3);
}

.card-back {
  background: linear-gradient(135deg, #ff6b6b, #ee5a24);
  color: white;
  transform: rotateY(0deg);
  z-index: 2;
}

.card-front {
  background: linear-gradient(135deg, #74b9ff, #0984e3);
  color: white;
  transform: rotateY(180deg);
  z-index: 1;
}

.card:not(.flipped):not(.matched):hover .card-inner {
  transform: scale(1.05);
}

.card.matched:hover .card-inner {
  transform: rotateY(180deg);
}

.card.matched .card-front {
  background: linear-gradient(135deg, #00b894, #00a085);
  animation: pulse 1s ease-in-out;
}

@keyframes pulse {
  0%,
  100% {
    transform: rotateY(180deg) scale(1);
  }
  50% {
    transform: rotateY(180deg) scale(1.2);
  }
}

@media (max-width: 768px) {
  .card {
    width: 60px;
    height: 60px;
  }

  .card-face {
    font-size: 1.5rem;
  }
}
</style>
