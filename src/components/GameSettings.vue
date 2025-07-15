<!-- GameSettings.vue -->
<template>
  <div class="game-settings">
    <div class="setting-item">
      遊戲模式：
      <select :value="gameMode" @change="handleChange">
        <option value="2x2">2×2 (4張 - 測試用)</option>
        <option value="6x5">6×5 (30張)</option>
        <option value="7x6">7×6 (42張)</option>
        <option value="8x6">8×6 (48張)</option>
      </select>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameSettings',
  props: {
    gameMode: {
      type: String,
      required: true,
    },
  },
  emits: ['update:gameMode', 'change'],
  methods: {
    handleChange(event) {
      this.$emit('update:gameMode', event.target.value)
      this.$emit('change')
    },
  },
}
</script>

<style scoped>
.game-settings {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.setting-item {
  background: rgba(255, 255, 255, 0.2);
  padding: 15px 25px;
  border-radius: 15px;
  color: white;
  font-weight: bold;
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.setting-item select {
  background: rgba(255, 255, 255, 0.9);
  border: none;
  padding: 8px 12px;
  border-radius: 8px;
  margin-left: 10px;
  font-weight: bold;
}
</style>
