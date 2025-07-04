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
  /* 移除所有背景相關屬性 */
  padding: 30px;
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
    background: rgba(255, 255, 255, 0.05);
    border-radius: 15px;
    padding: 20px;
    backdrop-filter: blur(5px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    width: 100%;
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
    overflow: hidden; /* 防止內容溢出 */
  }

  /* 橫屏模式下統計卡片的特殊樣式 */
  .landscape-sidebar .game-stats {
    width: 100%;
  }

  .landscape-sidebar .stats-container {
    display: grid !important;
    grid-template-columns: minmax(60px, 2fr) minmax(40px, 1fr) minmax(40px, 1fr) minmax(50px, 1.5fr);
    gap: 6px;
    width: 100%;
    min-width: 0;
  }

  .landscape-sidebar .stat-card {
    font-size: 0.7rem !important;
    padding: 4px 2px !important;
    text-align: center;
    min-width: 0;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  .landscape-sidebar .stat-card .stat-value {
    font-size: 0.8rem !important;
    font-weight: bold;
    line-height: 1;
    margin-bottom: 2px;
  }

  .landscape-sidebar .stat-card .stat-label {
    font-size: 0.6rem !important;
    line-height: 1;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    width: 100%;
  }

  /* 橫屏模式下控制區域的特殊樣式 */
  .landscape-sidebar .game-controls {
    width: 100%;
    min-width: 0;
  }

  .landscape-sidebar .controls-container {
    display: flex !important;
    flex-direction: column;
    gap: 6px;
    width: 100%;
    min-width: 0;
  }

  .landscape-sidebar .mode-section {
    display: flex !important;
    flex-wrap: wrap;
    gap: 6px;
    align-items: center;
    width: 100%;
    min-width: 0;
  }

  .landscape-sidebar .mode-label {
    font-size: 0.75rem;
    white-space: nowrap;
    min-width: fit-content;
    flex-shrink: 0;
  }

  .landscape-sidebar .mode-select {
    flex: 1;
    min-width: 100px;
    font-size: 0.7rem !important;
    padding: 4px 6px !important;
  }

  .landscape-sidebar .restart-button {
    width: 100% !important;
    margin-top: 4px;
    font-size: 0.75rem !important;
    padding: 6px !important;
  }

  /* 通用的橫屏響應式優化 */
  .landscape-sidebar * {
    box-sizing: border-box;
  }

  /* 針對不同寬度的適應性調整 */
  @media (orientation: landscape) and (max-height: 600px) {
    /* 超小寬度：改為2×2佈局 */
    @media (max-width: 800px) {
      .landscape-sidebar .stats-container {
        grid-template-columns: 1fr 1fr !important;
        grid-template-rows: 1fr 1fr;
        gap: 4px;
      }

      .landscape-sidebar .stat-card:nth-child(1) {
        grid-column: 1;
        grid-row: 1;
      }
      .landscape-sidebar .stat-card:nth-child(2) {
        grid-column: 2;
        grid-row: 1;
      }
      .landscape-sidebar .stat-card:nth-child(3) {
        grid-column: 1;
        grid-row: 2;
      }
      .landscape-sidebar .stat-card:nth-child(4) {
        grid-column: 2;
        grid-row: 2;
      }

      .landscape-sidebar .mode-section {
        flex-direction: column !important;
        align-items: stretch;
      }

      .landscape-sidebar .mode-label {
        text-align: center;
        font-size: 0.7rem;
      }

      .landscape-sidebar .mode-select {
        width: 100%;
        min-width: auto;
      }
    }

    /* 極小寬度：進一步壓縮 */
    @media (max-width: 650px) {
      .landscape-sidebar {
        padding: 12px !important;
        gap: 8px;
      }

      .landscape-sidebar .game-title {
        font-size: 1rem !important;
        margin-bottom: 8px;
      }

      .landscape-sidebar .stat-card {
        padding: 3px 1px !important;
        font-size: 0.6rem !important;
      }

      .landscape-sidebar .stat-card .stat-value {
        font-size: 0.7rem !important;
      }

      .landscape-sidebar .stat-card .stat-label {
        font-size: 0.55rem !important;
      }

      .landscape-sidebar .mode-label {
        font-size: 0.65rem;
      }

      .landscape-sidebar .restart-button {
        font-size: 0.7rem !important;
        padding: 5px !important;
      }
    }
  }

  .app {
    padding: 10px;
    align-items: flex-start;
  }

  .game-container {
    display: grid !important;
    grid-template-columns: minmax(280px, 35%) 1fr;
    gap: 15px;
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
  /* 強制顯示直屏內容，隱藏橫屏側邊欄 */
  .portrait-content {
    display: block !important;
    width: 100%;
  }

  .landscape-sidebar {
    display: none !important;
  }

  .app {
    padding: 10px;
    width: 100%;
    min-height: 100vh;
  }

  .game-container {
    padding: 20px;
    width: 100%;
    max-width: 100%;
    /* 確保手機版完美置中 */
    display: flex !important;
    flex-direction: column !important;
    align-items: center;
    justify-content: flex-start;
    grid-template-columns: none !important;
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
    .portrait-content {
      display: block !important;
    }

    .landscape-sidebar {
      display: none !important;
    }

    .app {
      padding: 5px;
    }

    .game-container {
      padding: 15px;
      display: flex !important;
      flex-direction: column !important;
      grid-template-columns: none !important;
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
