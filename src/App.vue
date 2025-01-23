<template>
  <div v-if="loading" class="loading-screen">
    <h2>游戏加载中...</h2>
    <el-progress 
      :percentage="loadingProgress" 
      :duration="3000"
      :stroke-width="20"
      status="success"
    />
  </div>
  <div v-else class="game-container">
    <el-card class="game-card">
      <div class="header">
        <h1>森林冒险</h1>
        <el-progress :percentage="health" :format="format" class="health-bar" />
      </div>

      <div class="story-text">
        {{ currentScene.text }}
      </div>

      <div class="choices">
        <el-button
          v-for="(choice, index) in currentScene.choices"
          :key="index"
          type="primary"
          @click="makeChoice(choice)"
          class="choice-button"
        >
          {{ choice.text }}
        </el-button>
      </div>

      <div v-if="gameOver" class="game-over">
        <h2>游戏结束</h2>
        <el-button type="success" @click="restartGame">再玩一次</el-button>
      </div>
    </el-card>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const health = ref(100)
const currentSceneId = ref('start')
const gameOver = ref(false)
const loading = ref(true)
const loadingProgress = ref(0)

const format = (percentage) => `生命值: ${percentage}%`

const scenes = {
  start: {
    text: '你发现自己站在一片黑暗森林的入口。路分成两个方向。',
    choices: [
      {
        text: '走左边的路',
        nextScene: 'leftPath',
        healthEffect: 0
      },
      {
        text: '走右边的路',
        nextScene: 'rightPath',
        healthEffect: 0
      }
    ]
  },
  leftPath: {
    text: '你遇到了一只受伤的狼。它用恳求的眼神看着你。',
    choices: [
      {
        text: '尝试帮助狼',
        nextScene: 'helpWolf',
        healthEffect: -10
      },
      {
        text: '慢慢后退',
        nextScene: 'backAway',
        healthEffect: 0
      }
    ]
  },
  rightPath: {
    text: '你发现了一间老旧的小屋。烟囱里冒着烟。',
    choices: [
      {
        text: '敲门',
        nextScene: 'cabin',
        healthEffect: 0
      },
      {
        text: '透过窗户看里面',
        nextScene: 'window',
        healthEffect: -20
      }
    ]
  },
  helpWolf: {
    text: '狼让你帮助了它，并成为了你忠实的伙伴！你赢了！',
    choices: [],
    gameOver: true
  },
  backAway: {
    text: '你离开了那只狼。内疚感一直困扰着你...',
    choices: [],
    gameOver: true
  },
  cabin: {
    text: '一位友善的女巫邀请你进去喝茶，听她讲魔法故事！',
    choices: [],
    gameOver: true
  },
  window: {
    text: '你滑倒了！女巫发现了你，把你变成了一只青蛙！',
    choices: [],
    gameOver: true
  }
}

const currentScene = computed(() => scenes[currentSceneId.value])

const makeChoice = (choice) => {
  health.value = Math.max(0, health.value + choice.healthEffect)
  currentSceneId.value = choice.nextScene
  
  if (health.value <= 0 || currentScene.value.gameOver) {
    gameOver.value = true
  }
}

const restartGame = () => {
  health.value = 100
  currentSceneId.value = 'start'
  gameOver.value = false
}

onMounted(() => {
  const interval = setInterval(() => {
    loadingProgress.value += 4
    if (loadingProgress.value >= 100) {
      clearInterval(interval)
      setTimeout(() => {
        loading.value = false
      }, 500)
    }
  }, 100)
})
</script>

<style scoped>
.loading-screen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #f5f7fa;
  z-index: 1000;
}

.loading-screen h2 {
  margin-bottom: 2rem;
  color: #409EFF;
}

.loading-screen :deep(.el-progress) {
  width: 300px;
}

.game-container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 0 1rem;
}

.game-card {
  background-color: #f5f7fa;
}

.header {
  margin-bottom: 1rem;
}

.health-bar {
  margin: 1rem 0;
}

.story-text {
  font-size: 1.2rem;
  margin: 2rem 0;
  line-height: 1.6;
}

.choices {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.choice-button {
  width: 100%;
}

.game-over {
  text-align: center;
  margin-top: 2rem;
}

h1 {
  text-align: center;
  color: #409EFF;
  margin: 0;
}

h2 {
  color: #F56C6C;
  margin-bottom: 1rem;
}
</style>