<template>
  <div class="app">
    <div class="game-container">
      <h1 class="game-title">🎯 記憶卡牌遊戲</h1>

      <div class="game-settings">
        <div class="setting-item">
          遊戲模式：
          <select v-model="gameMode" @change="resetGame">
            <option value="2x2">2×2 (4張 - 測試用)</option>
            <option value="4x3">4×3 (12張 - 簡單)</option>
            <option value="4x4">4×4 (16張 - 中等)</option>
          </select>
        </div>
      </div>

      <GameStats
        :game-time="gameTime"
        :correct-matches="correctMatches"
        :wrong-matches="wrongMatches"
        :accuracy="accuracy"
      />

      <GameControls :game-started="gameStarted" @start-game="startGame" @reset-game="resetGame" />

      <GameBoard v-if="gameStarted" :cards="cards" :game-mode="gameMode" @flip-card="flipCard" />

      <VictoryModal
        v-if="showVictory"
        :final-time="finalTime"
        :correct-matches="correctMatches"
        :wrong-matches="wrongMatches"
        :accuracy="accuracy"
        @close="closeVictory"
      />
    </div>
  </div>
</template>

<script>
import GameStats from './components/GameStats.vue'
import GameControls from './components/GameControls.vue'
import GameBoard from './components/GameBoard.vue'
import VictoryModal from './components/VictoryModal.vue'

export default {
  name: 'App',
  components: {
    GameStats,
    GameControls,
    GameBoard,
    VictoryModal,
  },
  data() {
    return {
      gameMode: '4x3',
      cards: [],
      flippedCards: [],
      gameStarted: false,
      gameTime: 0,
      gameTimer: null,
      correctMatches: 0,
      wrongMatches: 0,
      showVictory: false,
      finalTime: 0,
      // 使用更精美的 emoji 圖案
      emojis: [
        '🌟',
        '⭐',
        '✨',
        '💎',
        '🔥',
        '❤️',
        '💜',
        '💙',
        '🌈',
        '🦄',
        '🎯',
        '🎪',
        '🎨',
        '🎭',
        '🎪',
        '🎊',
        '🏆',
        '🥇',
        '👑',
        '💰',
        '🎁',
        '🎉',
        '🌺',
        '🌸',
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
      const modeMap = { '2x2': 4, '4x3': 12, '4x4': 16 }
      const total = modeMap[this.gameMode]
      const pairs = total / 2

      if (pairs > this.emojis.length) {
        console.error(`需要 ${pairs} 對表情符號，但只有 ${this.emojis.length} 個`)
        return
      }

      const selected = this.emojis.slice(0, pairs)
      const cards = selected.flatMap((emoji) => [
        { emoji, flipped: false, matched: false },
        { emoji, flipped: false, matched: false },
      ])
      this.cards = this.shuffle(cards)
    },
    shuffle(array) {
      const shuffled = [...array]
      for (let i = shuffled.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1))
        ;[shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]]
      }
      return shuffled
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

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Arial', sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  overflow-x: hidden;
}

.app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px;
}

.game-container {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(20px);
  border-radius: 20px;
  padding: 20px;
  box-shadow:
    0 20px 40px rgba(0, 0, 0, 0.1),
    0 0 0 1px rgba(255, 255, 255, 0.2);
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
}

.game-title {
  text-align: center;
  color: #2d3748;
  font-size: clamp(1.5rem, 4vw, 2rem);
  margin-bottom: 20px;
  font-weight: 700;
  letter-spacing: -0.5px;
}

.game-settings {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.setting-item {
  background: rgba(102, 126, 234, 0.1);
  padding: 12px 20px;
  border-radius: 12px;
  color: #2d3748;
  font-weight: 600;
  font-size: 14px;
  border: 1px solid rgba(102, 126, 234, 0.2);
}

.setting-item select {
  background: white;
  border: 1px solid #e2e8f0;
  padding: 6px 10px;
  border-radius: 8px;
  margin-left: 8px;
  font-weight: 500;
  color: #2d3748;
  cursor: pointer;
  transition: border-color 0.2s ease;
}

.setting-item select:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

/* 響應式設計 */
@media (max-width: 768px) {
  .app {
    padding: 15px;
    align-items: flex-start;
    padding-top: 20px;
  }

  .game-container {
    padding: 16px;
    border-radius: 16px;
    margin-top: 0;
  }

  .game-title {
    font-size: 1.75rem;
    margin-bottom: 16px;
  }

  .setting-item {
    padding: 10px 16px;
    font-size: 13px;
  }

  .setting-item select {
    padding: 5px 8px;
    font-size: 13px;
  }
}

@media (max-width: 480px) {
  .app {
    padding: 10px;
  }

  .game-container {
    padding: 12px;
    border-radius: 12px;
  }

  .game-title {
    font-size: 1.5rem;
    margin-bottom: 12px;
  }
}

@media (min-width: 1200px) {
  .game-container {
    max-width: 600px;
    padding: 30px;
  }
}
</style>
