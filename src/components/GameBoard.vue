<template>
  <div class="game-board" :class="`board-${gameMode}`">
    <div
      v-for="(card, index) in cards"
      :key="index"
      class="card"
      :class="{ flipped: card.flipped || card.matched, matched: card.matched }"
      @click="handleClick(index)"
    >
      <div class="card-inner">
        <div class="card-face card-back">
          <div class="card-pattern">
            <div class="pattern-circle"></div>
            <div class="pattern-diamond"></div>
            <div class="pattern-star">✦</div>
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
  methods: {
    handleClick(index) {
      if (!this.cards[index].matched && !this.cards[index].flipped) {
        this.$emit('flipCard', index)
      }
    },
  },
}
</script>

<style scoped>
/* 保持你原有的佈局結構，完全不動 */
.game-board {
  display: grid;
  justify-content: center;
  gap: 12px;
  padding: 10px;
}

.board-2x2 {
  grid-template-columns: repeat(2, 1fr);
}
.board-4x3 {
  grid-template-columns: repeat(4, 1fr);
}
.board-4x4 {
  grid-template-columns: repeat(4, 1fr);
}

.card {
  width: 75px;
  height: 75px;
  perspective: 1000px;
  position: relative;
  cursor: pointer;
}

.card-inner {
  position: relative; /* ✅ 保持你的關鍵修正 */
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.3s ease-in-out;
}

.card.flipped .card-inner {
  transform: rotateY(180deg);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2rem;
  border-radius: 10px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

/* 只美化背面，不改變任何定位 */
.card-back {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  overflow: hidden;
}

/* 簡單的光澤效果，不影響佈局 */
.card-back::before {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: linear-gradient(
    45deg,
    transparent 40%,
    rgba(255, 255, 255, 0.1) 50%,
    transparent 60%
  );
  animation: shimmer 4s infinite;
  pointer-events: none;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%) translateY(-100%);
  }
  100% {
    transform: translateX(100%) translateY(100%);
  }
}

/* 美化圖案但不影響佈局 */
.card-pattern {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 3px;
  position: relative;
  z-index: 1;
}

.pattern-circle {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.7);
}

.pattern-diamond {
  width: 8px;
  height: 8px;
  background: rgba(255, 255, 255, 0.8);
  transform: rotate(45deg);
}

.pattern-star {
  color: rgba(255, 255, 255, 0.9);
  font-size: 10px;
}

.card-front {
  background-color: #f3f3f3;
  transform: rotateY(180deg);
  color: #2d3748;
}

.card.matched .card-front {
  background: linear-gradient(135deg, #48bb78 0%, #38a169 100%);
  color: white;
  font-weight: bold;
}

/* 簡單的 hover 效果，不影響定位 */
.card:hover {
  transform: scale(1.02);
}

.card:active {
  transform: scale(0.98);
}
</style>
