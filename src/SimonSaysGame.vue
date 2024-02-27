<script setup>
import { ref } from 'vue'
import SimonSaysButton from './components/SimonSaysButton.vue'

const colors = ['red', 'green', 'blue', 'yellow']
const mode = {
  easy: 1500,
  medium: 1000,
  hard: 400,
}
const sequence = ref([])
const round = ref(0)
const isSequenceEnd = ref(false)
const flashColor = ref()
const currentIndex = ref(0)
const audioPlayer = ref(null)
const activeMode = ref('easy')

const startGame = () => {
  sequence.value = [] // Сбрасываем последовательность
  round.value = 0 // Cбрасываем счетчик раунда
  nextRound() // Начинаем раунд
}

const endGame = () => {
  sequence.value = []
  round.value = 0
}

const nextRound = async () => {
  round.value++
  sequence.value.push(colors[randomNumber()]) //Добавляем случайный цвет из доступных
  await sleep(1000)
  playSequence() // Воспроизводим последовательность
}

const playSequence = async () => {
  for (let i = 0; i < sequence.value.length; i++) {
    playSound(sequence.value[i]) // Воспроизводим звук в зависимости от цвета в последовательности
    await hightLight(sequence.value[i]) // Подсвечиваем цвета из последовательности
  }
  isSequenceEnd.value = true
}

const hightLight = async (color) => {
  flashColor.value = color
  await sleep(mode[activeMode.value]) // Подождать некоторое время перед подсветкой следующей кнопки
  flashColor.value = '' // Сбрасываем подсветку кнопки
  await sleep(100) // Подождать некоторое время перед подсветкой следующей кнопки
}

const playSound = (color) => {
  const index = colors.indexOf(color) + 1
  audioPlayer.value.src = `/src/assets/audio/sounds_${index}.mp3`
}

const sleep = (ms) => {
  return new Promise((resolve) => setTimeout(resolve, ms))
}

const randomNumber = () => {
  return Math.floor(Math.random() * colors.length)
}

const handleButtonClick = (color) => {
  if (!isSequenceEnd.value) {
    return
  }

  if (color === sequence.value[currentIndex.value]) {
    currentIndex.value++
    playSound(color)

    if (currentIndex.value === sequence.value.length) {
      currentIndex.value = 0
      isSequenceEnd.value = !isSequenceEnd.value
      nextRound()
    }
  } else {
    endGame()
  }
}

const handleRadioClick = (event) => {
  if (activeMode == event.target.value) {
    return
  }
  endGame()
}
</script>

<template>
  <div class="app">
    <div class="header">
      <h1>Simon says</h1>
      <h2 class="game-info__title">Round: {{ round }}</h2>
    </div>

    <div class="container">
      <ul>
        <SimonSaysButton
          v-for="(color, index) in colors"
          :key="index"
          :color="color"
          @click="handleButtonClick"
          :isFlashing="color === flashColor"
        />
      </ul>

      <div>
        <div class="game-options">
          <h2>Game Options</h2>
          <div class="game-options__radio" v-for="(item, key) in mode">
            <input
              :key="key"
              type="radio"
              :id="key"
              name="mode"
              :value="key"
              v-model="activeMode"
              @input="handleRadioClick"
            />
            <label :for="key">{{ key }}</label>
          </div>
        </div>
        <button @click="startGame()" class="game-start">Start</button>
      </div>
    </div>
    <audio ref="audioPlayer" autoplay></audio>
  </div>
</template>

<style scoped>
.app {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.header {
  margin-bottom: 50px;
  text-align: center;
}

.container {
  display: flex;
  gap: 100px;
}

ul {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  background: rgb(227, 227, 227);
  border-radius: 100%;
  height: 500px;
  width: 500px;
  place-items: center;
  overflow: hidden;
  margin-bottom: 20px;
}

.game-options {
  margin-bottom: 20px;
}

.game-options__radio {
  display: flex;
  align-items: center;
  gap: 10px;
  text-transform: uppercase;
  font-size: 16px;
}
.game-options__radio label {
  cursor: pointer;
}

.game-start {
  width: 5em;
  box-sizing: border-box;
  font-size: 1.4em;
  -webkit-border-radius: 10px 10px 10px 10px;
  border-radius: 10px 10px 10px 10px;
  background: #6dabe8;
  border: none;
  padding: 0.3em 0.6em;
}
</style>
