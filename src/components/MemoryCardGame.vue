<template>
  <div class="game-container">
    <GameHeader />

    <GameSettings v-model:gameMode="gameMode" @change="resetGame" />

    <GameStats
      :gameTime="gameTime"
      :correctMatches="correctMatches"
      :wrongMatches="wrongMatches"
      :accuracy="accuracy"
    />

    <GameControls :gameStarted="gameStarted" @start="startGame" @reset="resetGame" />

    <GameBoard v-if="gameStarted" :cards="cards" :gameMode="gameMode" @flip-card="flipCard" />

    <VictoryModal
      v-if="showVictory"
      :finalTime="finalTime"
      :correctMatches="correctMatches"
      :wrongMatches="wrongMatches"
      :accuracy="accuracy"
      @close="closeVictory"
    />
  </div>
</template>

<script>
import GameHeader from './GameHeader.vue'
import GameSettings from './GameSettings.vue'
import GameStats from './GameStats.vue'
import GameControls from './GameControls.vue'
import GameBoard from './GameBoard.vue'
import VictoryModal from './VictoryModal.vue'

export default {
  name: 'MemoryCardGame',
  components: {
    GameHeader,
    GameSettings,
    GameStats,
    GameControls,
    GameBoard,
    VictoryModal,
  },
  data() {
    return {
      gameMode: '2x2',
      cards: [],
      flippedCards: [],
      gameStarted: false,
      gameTime: 0,
      gameTimer: null,
      correctMatches: 0,
      wrongMatches: 0,
      showVictory: false,
      finalTime: 0,
      emojis: [
        '🐱',
        '🐶',
        '🐭',
        '🐹',
        '🐰',
        '🦊',
        '🐻',
        '🐼',
        '🐨',
        '🐯',
        '🦁',
        '🐮',
        '🐷',
        '🐸',
        '🐵',
        '🐔',
        '🐧',
        '🐦',
        '🐤',
        '🦆',
        '🦅',
        '🦉',
        '🦇',
        '🐺',
        '🐗',
        '🐴',
        '🦄',
        '🐝',
        '🐛',
        '🦋',
        '🐌',
        '🐞',
      ],
    }
  },
  computed: {
    accuracy() {
      const total = this.correctMatches + this.wrongMatches
      return total === 0 ? 100 : Math.round((this.correctMatches / total) * 100)
    },
  },
  methods: {
    startGame() {
      this.gameStarted = true
      this.setupCards()
      this.startTimer()
    },
    resetGame() {
      this.gameStarted = false
      this.stopTimer()
      this.gameTime = 0
      this.correctMatches = 0
      this.wrongMatches = 0
      this.flippedCards = []
      this.showVictory = false
      this.cards = []
    },
    setupCards() {
      const modeMap = { '2x2': 4, '6x5': 30, '7x6': 42, '8x6': 48 }
      const total = modeMap[this.gameMode]
      const pairs = total / 2
      const selected = this.emojis.slice(0, pairs)
      const cards = selected.flatMap((emoji) => [
        { emoji, flipped: false, matched: false },
        { emoji, flipped: false, matched: false },
      ])
      this.cards = this.shuffle(cards)
    },
    shuffle(array) {
      return array.sort(() => Math.random() - 0.5)
    },
    flipCard(index) {
      const card = this.cards[index]
      if (card.flipped || card.matched || this.flippedCards.length >= 2) return
      card.flipped = true
      this.flippedCards.push(index)
      if (this.flippedCards.length === 2) this.checkMatch()
    },
    checkMatch() {
      const [i1, i2] = this.flippedCards
      const c1 = this.cards[i1],
        c2 = this.cards[i2]
      setTimeout(() => {
        if (c1.emoji === c2.emoji) {
          c1.matched = c2.matched = true
          this.correctMatches++
          if (this.cards.every((c) => c.matched)) this.gameWon()
        } else {
          c1.flipped = c2.flipped = false
          this.wrongMatches++
        }
        this.flippedCards = []
      }, 1000)
    },
    gameWon() {
      this.stopTimer()
      this.finalTime = this.gameTime
      this.showVictory = true
    },
    closeVictory() {
      this.showVictory = false
    },
    startTimer() {
      this.gameTimer = setInterval(() => this.gameTime++, 1000)
    },
    stopTimer() {
      clearInterval(this.gameTimer)
      this.gameTimer = null
    },
  },
  beforeUnmount() {
    this.stopTimer()
  },
}
</script>

<style scoped>
.game-container {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 30px;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.2);
  max-width: 1200px;
  width: 100%;
}
</style>
