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
  position: relative; /* ✅ 關鍵修正，讓卡片翻轉不會跳動 */
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

.card-back {
  background-color: #4a90e2;
  color: white;
}

.card-front {
  background-color: #f3f3f3;
  transform: rotateY(180deg);
  color: #2d3748;
}

.card.matched .card-front {
  background-color: #48bb78;
  color: white;
  font-weight: bold;
}
</style>
