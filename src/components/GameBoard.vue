<template>
  <div class="game-board" :class="`board-${gameMode}`">
    <div
      v-for="(card, index) in cards"
      :key="index"
      class="card"
      :class="{ flipped: card.flipped || card.matched, matched: card.matched }"
      @click="$emit('flipCard', index)"
    >
      <div class="card-inner">
        <div class="card-face card-back">
          <div class="card-pattern">
            <div class="pattern-line"></div>
            <div class="pattern-line"></div>
            <div class="pattern-line"></div>
          </div>
        </div>
        <div class="card-face card-front">{{ card.emoji }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameBoard',
  props: {
    cards: {
      type: Array,
      required: true,
    },
    gameMode: {
      type: String,
      required: true,
    },
  },
  emits: ['flipCard'],
}
</script>

<style scoped>
.game-board {
  display: grid;
  justify-content: center;
  margin: 20px auto;
  gap: clamp(8px, 2vw, 16px);
  max-width: 100%;
  padding: 0 10px;
}

/* 響應式網格佈局 */
.board-2x2 {
  grid-template-columns: repeat(2, 1fr);
  max-width: 200px;
}

.board-4x3 {
  grid-template-columns: repeat(4, 1fr);
  max-width: 320px;
}

.board-4x4 {
  grid-template-columns: repeat(4, 1fr);
  max-width: 320px;
}

.card {
  width: clamp(60px, 15vw, 75px);
  height: clamp(60px, 15vw, 75px);
  position: relative;
  perspective: 1000px;
  cursor: pointer;
  transition: transform 0.2s ease;
}

.card:hover {
  transform: scale(1.05);
}

.card:active {
  transform: scale(0.95);
}

.card-inner {
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1);
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
  border-radius: clamp(8px, 2vw, 12px);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: clamp(1.2rem, 4vw, 2rem);
  font-weight: bold;
  box-shadow:
    0 4px 12px rgba(0, 0, 0, 0.15),
    0 0 0 1px rgba(255, 255, 255, 0.1);
  border: 2px solid transparent;
  transition: all 0.3s ease;
}

.card-back {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 30%, #667eea 60%, #764ba2 100%);
  transform: rotateY(0deg);
  z-index: 2;
  position: relative;
  overflow: hidden;
}

.card-back::before {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: linear-gradient(
    45deg,
    transparent 30%,
    rgba(255, 255, 255, 0.1) 50%,
    transparent 70%
  );
  transform: rotate(45deg);
  animation: shimmer 3s infinite;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%) translateY(-100%) rotate(45deg);
  }
  100% {
    transform: translateX(100%) translateY(100%) rotate(45deg);
  }
}

.card-pattern {
  position: relative;
  width: 30px;
  height: 30px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}

.pattern-line {
  width: 100%;
  height: 2px;
  background: rgba(255, 255, 255, 0.3);
  border-radius: 1px;
  transform: skew(-15deg);
}

.card-front {
  background: linear-gradient(135deg, #ffffff 0%, #f8fafc 100%);
  color: transparent;
  text-shadow: 0 0 0 #2d3748;
  transform: rotateY(180deg);
  z-index: 1;
  border: 2px solid #e2e8f0;
}

.card:not(.flipped):not(.matched):hover .card-back {
  box-shadow:
    0 8px 25px rgba(0, 0, 0, 0.2),
    0 0 0 2px rgba(255, 255, 255, 0.2);
}

.card.matched .card-front {
  background: linear-gradient(135deg, #48bb78 0%, #38a169 100%);
  border-color: #38a169;
  animation: matchPulse 0.6s ease-out;
}

.card.matched .card-front::after {
  content: '✨';
  position: absolute;
  top: 5px;
  right: 5px;
  font-size: 0.8rem;
  opacity: 0.7;
}

@keyframes matchPulse {
  0% {
    transform: rotateY(180deg) scale(1);
    box-shadow: 0 0 0 0 rgba(72, 187, 120, 0.7);
  }
  50% {
    transform: rotateY(180deg) scale(1.1);
    box-shadow: 0 0 0 10px rgba(72, 187, 120, 0);
  }
  100% {
    transform: rotateY(180deg) scale(1);
    box-shadow: 0 0 0 0 rgba(72, 187, 120, 0);
  }
}

/* 手機優化 */
@media (max-width: 480px) {
  .game-board {
    gap: clamp(6px, 2vw, 12px);
    margin: 15px auto;
  }

  .card {
    width: clamp(50px, 18vw, 65px);
    height: clamp(50px, 18vw, 65px);
  }

  .card-face {
    font-size: clamp(1rem, 5vw, 1.5rem);
    border-radius: 8px;
  }

  .board-4x3,
  .board-4x4 {
    max-width: 280px;
  }
}

/* 平板優化 */
@media (min-width: 481px) and (max-width: 768px) {
  .card {
    width: clamp(65px, 12vw, 70px);
    height: clamp(65px, 12vw, 70px);
  }
}

/* 桌面優化 */
@media (min-width: 1024px) {
  .game-board {
    gap: 16px;
  }

  .card {
    width: 75px;
    height: 75px;
  }

  .board-4x3,
  .board-4x4 {
    max-width: 340px;
  }
}
</style>
