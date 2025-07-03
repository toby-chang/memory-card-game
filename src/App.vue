.game-container { background: rgba(255, 255, 255, 0.05); backdrop-filter: blur(10px); border-radius:
20px; padding: 30px; box-shadow: 0 25px 50px rgba(0, 0, 0, 0.3), 0 0 0 1px rgba(255, 255, 255, 0.1);
border: 1px solid rgba(255, 255, 255, 0.1); max-width: 1200px; width: 100%; text-align: center; }
<template>
  <div class="app">
    <div class="game-container">
      <!-- 直屏佈局 -->
      <div class="portrait-content">
        <h1 class="game-title">🎴 記憶卡牌遊戲</h1>

        <GameStats
          :game-time="gameTime"
          :correct-matches="correctMatches"
          :wrong-matches="wrongMatches"
          :accuracy="accuracy"
        />

        <GameControls :game-mode="gameMode" @change-mode="changeGameMode" @restart="restartGame" />
      </div>

      <!-- 橫屏左側信息欄 -->
      <div class="landscape-sidebar">
        <h1 class="game-title">🎴 記憶卡牌遊戲</h1>

        <GameStats
          :game-time="gameTime"
          :correct-matches="correctMatches"
          :wrong-matches="wrongMatches"
          :accuracy="accuracy"
        />

        <GameControls :game-mode="gameMode" @change-mode="changeGameMode" @restart="restartGame" />
      </div>

      <GameBoard :cards="cards" :game-mode="gameMode" @flip-card="flipCard" />

      <VictoryModal
        v-if="showVictory"
        :game-time="gameTime"
        :correct-matches="correctMatches"
        :wrong-matches="wrongMatches"
        :accuracy="accuracy"
        @restart="restartGame"
        @close="showVictory = false"
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
      gameTime: 0,
      correctMatches: 0,
      wrongMatches: 0,
      showVictory: false,
      timer: null,
      isGameStarted: false,
      canFlip: true,

      // 圖片路徑陣列 (使用相對路徑，跳過 D0)
      cardImages: [
        './img/diamonds/D1.png',
        './img/diamonds/D2.png',
        './img/diamonds/D3.png',
        './img/diamonds/D4.png',
        './img/diamonds/D5.png',
        './img/diamonds/D6.png',
        './img/diamonds/D7.png',
        './img/diamonds/D8.png',
        './img/diamonds/D9.png',
        './img/diamonds/D10.png',
        './img/diamonds/J10.png',
        './img/diamonds/K10.png',
        './img/diamonds/Q10.png',
        // 如果還有更多圖片可以繼續加
      ],
    }
  },
  computed: {
    accuracy() {
      const total = this.correctMatches + this.wrongMatches
      return total === 0 ? 100 : Math.round((this.correctMatches / total) * 100)
    },
    gameComplete() {
      return this.cards.every((card) => card.matched)
    },
  },
  mounted() {
    this.initializeGame()
  },
  beforeUnmount() {
    if (this.timer) {
      clearInterval(this.timer)
    }
  },
  methods: {
    initializeGame() {
      this.resetGameStats()
      this.createCards()
    },

    resetGameStats() {
      this.gameTime = 0
      this.correctMatches = 0
      this.wrongMatches = 0
      this.flippedCards = []
      this.showVictory = false
      this.isGameStarted = false
      this.canFlip = true

      if (this.timer) {
        clearInterval(this.timer)
        this.timer = null
      }
    },

    createCards() {
      const modes = {
        '2x2': 4,
        '4x3': 12,
        '4x4': 16,
      }

      const totalCards = modes[this.gameMode]
      const pairsNeeded = totalCards / 2

      // 確保有足夠的圖片
      if (pairsNeeded > this.cardImages.length) {
        console.warn(`需要 ${pairsNeeded} 種圖片，但只有 ${this.cardImages.length} 種`)
      }

      // 選擇需要的圖片
      const selectedImages = this.cardImages.slice(0, pairsNeeded)

      // 創建配對的卡牌
      const cardPairs = selectedImages.flatMap((image) => [
        { id: Math.random(), image, flipped: false, matched: false },
        { id: Math.random(), image, flipped: false, matched: false },
      ])

      // 洗牌
      this.cards = this.shuffleArray(cardPairs)
    },

    shuffleArray(array) {
      const shuffled = [...array]
      for (let i = shuffled.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1))
        ;[shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]]
      }
      return shuffled
    },

    flipCard(index) {
      if (!this.canFlip || this.cards[index].flipped || this.cards[index].matched) {
        return
      }

      // 開始計時
      if (!this.isGameStarted) {
        this.startTimer()
        this.isGameStarted = true
      }

      // 翻開卡牌
      this.cards[index].flipped = true
      this.flippedCards.push(index)

      // 檢查是否翻開兩張
      if (this.flippedCards.length === 2) {
        this.canFlip = false
        setTimeout(() => {
          this.checkMatch()
        }, 1000)
      }
    },

    checkMatch() {
      const [first, second] = this.flippedCards
      const firstCard = this.cards[first]
      const secondCard = this.cards[second]

      if (firstCard.image === secondCard.image) {
        // 配對成功
        firstCard.matched = true
        secondCard.matched = true
        this.correctMatches++

        // 檢查遊戲是否完成
        if (this.gameComplete) {
          this.endGame()
        }
      } else {
        // 配對失敗
        firstCard.flipped = false
        secondCard.flipped = false
        this.wrongMatches++
      }

      this.flippedCards = []
      this.canFlip = true
    },

    startTimer() {
      this.timer = setInterval(() => {
        this.gameTime++
      }, 1000)
    },

    endGame() {
      if (this.timer) {
        clearInterval(this.timer)
        this.timer = null
      }

      setTimeout(() => {
        this.showVictory = true
      }, 500)
    },

    changeGameMode(newMode) {
      this.gameMode = newMode
      this.initializeGame()
    },

    restartGame() {
      this.initializeGame()
    },
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
  font-family: 'Arial', sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 50%, #5b73e8 100%);
  min-height: 100vh;
  color: white;
}

.app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

.game-container {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 30px;
  box-shadow:
    0 25px 50px rgba(0, 0, 0, 0.3),
    0 0 0 1px rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.1);
  max-width: 1200px;
  width: 100%;
  text-align: center;
}

/* 佈局控制 - 默認只顯示直屏內容 */
.portrait-content {
  display: block;
  width: 100%;
}

.landscape-sidebar {
  display: none;
}

/* 橫屏時的佈局切換 */
@media (orientation: landscape) and (max-height: 600px) {
  /* 強制隱藏直屏內容 */
  .portrait-content {
    display: none !important;
  }

  /* 顯示橫屏側邊欄 */
  .landscape-sidebar {
    display: flex !important;
    flex-direction: column;
    gap: 15px;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 15px;
    padding: 20px;
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    width: 100%;
  }

  .app {
    padding: 10px;
    align-items: flex-start;
  }

  .game-container {
    display: grid !important;
    grid-template-columns: 300px 1fr;
    gap: 20px;
    text-align: left;
    padding: 15px;
    align-items: start;
  }

  .landscape-sidebar .game-title {
    font-size: 1.2rem;
    margin: 0 0 15px 0;
    text-align: center;
  }

  /* 確保 GameBoard 在右側正確顯示 */
  .game-container > div:last-child:not(.portrait-content):not(.landscape-sidebar) {
    justify-self: center;
    align-self: center;
  }
}

.game-title {
  font-size: clamp(1.8rem, 5vw, 2.5rem);
  margin-bottom: 30px;
  text-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
  background: linear-gradient(45deg, #ffffff, #f1f5f9, #e2e8f0);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* 手機優化 - 完美置中，橫豎屏都用垂直佈局 */
@media (max-width: 768px) {
  .app {
    padding: 10px;
    width: 100%;
    min-height: 100vh;
  }

  .game-container {
    padding: 20px;
    border-radius: 15px;
    width: 100%;
    max-width: 100%;
    /* 確保手機版完美置中 */
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
  }

  .game-title {
    margin-bottom: 20px;
    width: 100%;
    text-align: center;
  }

  /* 確保所有子元素都置中 */
  .game-container > * {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  /* 手機橫屏時只調整間距，保持垂直佈局 */
  @media (orientation: landscape) {
    .app {
      padding: 5px;
    }

    .game-container {
      padding: 15px;
    }

    .game-title {
      font-size: 1.5rem;
      margin-bottom: 15px;
    }
  }
}

/* 手機橫屏優化 */
@media (max-width: 768px) and (orientation: landscape) and (max-height: 500px) {
  .app {
    align-items: flex-start;
    padding: 5px;
  }

  .game-container {
    display: grid;
    grid-template-columns: 250px 1fr;
    grid-template-areas: 'info board';
    gap: 15px;
    padding: 15px;
    max-width: 100vw;
    text-align: left;
  }

  .mobile-info {
    grid-area: info;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .game-title {
    font-size: 1.2rem;
    margin-bottom: 10px;
    text-align: left;
  }

  .game-container > *:nth-child(4) {
    /* GameBoard */
    grid-area: board;
    justify-self: center;
  }
}
</style>
