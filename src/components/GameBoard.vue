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
        <div class="card-face card-front">
          <img :src="card.image" :alt="card.image" class="card-image" />
        </div>
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
  align-content: center;
  gap: 12px;
  padding: 10px;
  margin: 20px auto;
  width: 100%;
  max-width: fit-content;
  /* 確保完美置中 */
  justify-items: center;
  place-items: center;
  place-content: center;
}

.board-2x2 {
  grid-template-columns: repeat(2, 75px);
  grid-template-rows: repeat(2, 112px);
  justify-content: center;
}
.board-4x3 {
  grid-template-columns: repeat(4, 75px);
  grid-template-rows: repeat(3, 112px);
  justify-content: center;
}
.board-4x4 {
  grid-template-columns: repeat(4, 75px);
  grid-template-rows: repeat(4, 112px);
  justify-content: center;
}

.card {
  width: 75px;
  height: 112px; /* 改為 75 * 1.5 = 112.5，配合 2:3 比例 */
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

/* 清新卡背 */
.card-back {
  background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 50%, #6366f1 100%);
  color: white;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

/* 和諧圖案 - 確保不會透過去 */
.card-pattern {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 3px;
  position: relative;
  z-index: 1;
  opacity: 1;
  transition: opacity 0.3s ease; /* 添加透明度過渡 */
}

/* 翻轉時隱藏背面圖案 */
.card.flipped .card-pattern {
  opacity: 0;
}

.pattern-circle {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.8);
  box-shadow: 0 0 4px rgba(255, 255, 255, 0.4);
}

.pattern-diamond {
  width: 8px;
  height: 8px;
  background: rgba(255, 255, 255, 0.9);
  transform: rotate(45deg);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.pattern-star {
  color: rgba(255, 255, 255, 0.9);
  font-size: 10px;
  text-shadow: 0 0 2px rgba(255, 255, 255, 0.5);
}

.card-front {
  background: #ffffff;
  transform: rotateY(180deg);
  color: #1f2937;
  border: 3px solid transparent;
  padding: 0;
  overflow: hidden;
}

.card-image {
  width: 100%;
  height: 100%;
  object-fit: cover; /* 保持 cover 讓圖片完全填滿長方形卡牌 */
  border-radius: 7px;
}

.card.matched .card-front {
  border: 3px solid #10b981; /* 成功綠色邊框 */
  box-shadow:
    0 0 20px rgba(16, 185, 129, 0.6),
    0 0 40px rgba(16, 185, 129, 0.3); /* 更強的發光效果 */
  animation: matchSuccess 0.8s ease-out;
}

.card.matched .card-image {
  filter: brightness(1.1) contrast(1.05); /* 微亮化 */
}

/* 配對成功動畫 */
@keyframes matchSuccess {
  0% {
    transform: rotateY(180deg) scale(1);
    box-shadow:
      0 0 0 0 rgba(16, 185, 129, 0.8),
      0 0 0 0 rgba(16, 185, 129, 0.4);
  }
  50% {
    transform: rotateY(180deg) scale(1.1);
    box-shadow:
      0 0 25px rgba(16, 185, 129, 0.8),
      0 0 50px rgba(16, 185, 129, 0.4);
  }
  100% {
    transform: rotateY(180deg) scale(1);
    box-shadow:
      0 0 20px rgba(16, 185, 129, 0.6),
      0 0 40px rgba(16, 185, 129, 0.3);
  }
}

/* 簡單的 hover 效果，不影響定位 */
.card:hover {
  transform: scale(1.03) translateY(-2px);
}

.card:active {
  transform: scale(0.97);
}

/* 配對成功時額外的懸停效果 */
.card.matched:hover {
  transform: scale(1.05) translateY(-3px);
}

.card.matched:hover .card-front {
  box-shadow:
    0 0 30px rgba(16, 185, 129, 0.8),
    0 0 60px rgba(16, 185, 129, 0.4);
}
</style>
