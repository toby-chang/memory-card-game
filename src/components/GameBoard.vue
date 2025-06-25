<template>
  <div class="game-board" :class="`board-${gameMode}`">
    <GameCard
      v-for="(card, index) in cards"
      :key="index"
      :card="card"
      :index="index"
      @flip="$emit('flip-card', index)"
    />
  </div>
</template>

<script>
import GameCard from './GameCard.vue'

export default {
  name: 'GameBoard',
  components: {
    GameCard,
  },
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
  emits: ['flip-card'],
}
</script>

<style scoped>
.game-board {
  display: grid;
  gap: 15px;
  justify-content: center;
  margin-bottom: 30px;
}

.board-2x2 {
  grid-template-columns: repeat(2, 1fr);
}

.board-6x5 {
  grid-template-columns: repeat(6, 1fr);
}

.board-7x6 {
  grid-template-columns: repeat(7, 1fr);
}

.board-8x6 {
  grid-template-columns: repeat(8, 1fr);
}

@media (max-width: 768px) {
  .game-board {
    gap: 10px;
  }

  .board-6x5 {
    grid-template-columns: repeat(5, 1fr);
  }

  .board-7x6 {
    grid-template-columns: repeat(6, 1fr);
  }

  .board-8x6 {
    grid-template-columns: repeat(6, 1fr);
  }
}
</style>
