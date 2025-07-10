<template>
  <div class="main-container">
    <header>
      <h1>Bingo</h1>
    </header>

    <section id="display-digit" v-if="displayDigit > 0 && wininingLineNumber < 5">
      {{ displayDigit }}
    </section>
    <section id="display-win-match" v-if="wininingLineNumber >= 5">
      🎉🎊 Congretulation You Win with 5 Bingo Lines! 🥳🥳
    </section>
    <main v-for="(num, rowIndex) in getRows" :key="rowIndex">
      <div
        class="item"
        v-for="(numItem, colIndex) in num"
        :key="colIndex"
        :class="[
          matchedArray.includes(numItem) ? 'hightlight' : '',
          wonRows.includes(rowIndex) || wonColumns.includes(colIndex) ? 'winningLine' : '',
        ]"
      >
        {{ numItem }}
      </div>
    </main>

    <footer>
      <button id="start-btn" @click="gameStart">Start</button>
      <button id="refresh-btn" @click="refreshTheGame">Refresh</button>
    </footer>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref, watch, computed } from 'vue'

const bingoMatrix = ref<number[]>([])

const displayDigit = ref(0)

const matchedArray = ref<number[]>([])

const wininingLineNumber = ref<number>(0)

let interval: any = ref()

const wonRows = ref<number[]>([])
const wonColumns = ref<number[]>([])

const generateMatrixNumber = () => {
  while (bingoMatrix.value.length < 25) {
    const num = Math.floor(Math.random() * (99 - 1 + 1)) + 1

    if (!bingoMatrix.value.includes(num)) {
      bingoMatrix.value.push(num)
    }
  }
}

const getRows = computed(() => {
  const rows = []
  for (let i = 0; i < bingoMatrix.value.length; i += 5) {
    rows.push(bingoMatrix.value.slice(i, i + 5))
  }
  return rows
})

watch(
  matchedArray,
  () => {
    checkWin()
    if (wininingLineNumber.value >= 5) {
      clearInterval(interval.value)
    }
  },
  { deep: true },
)

const checkWin = () => {
  for (let r = 0; r < 5; r++) {
    if (wonRows.value.includes(r)) continue

    const row = bingoMatrix.value.slice(r * 5, r * 5 + 5)

    if (row.every((num) => matchedArray.value.includes(num))) {
      wonRows.value.push(r)
      wininingLineNumber.value += 1
    }
  }

  for (let c = 0; c < 5; c++) {
    if (wonColumns.value.includes(c)) continue

    const column = [
      bingoMatrix.value[c],
      bingoMatrix.value[c + 5],
      bingoMatrix.value[c + 10],
      bingoMatrix.value[c + 15],
      bingoMatrix.value[c + 20],
    ]

    if (column.every((num) => matchedArray.value.includes(num))) {
      wonColumns.value.push(c)
      wininingLineNumber.value += 1
    }
  }
}

function gameStart() {
  let i = 0

  const randomeDigitNumber = Math.floor(Math.random() * (99 - 1 + 1)) + 1

  displayDigit.value = randomeDigitNumber

  const existingNumber = matchedArray.value.includes(randomeDigitNumber)
  if (!existingNumber && bingoMatrix.value.includes(randomeDigitNumber)) {
    matchedArray.value.push(randomeDigitNumber)
  }

  interval.value = setInterval(() => {
    const randomeDigitNumber = Math.floor(Math.random() * (99 - 1 + 1)) + 1
    displayDigit.value = randomeDigitNumber

    const existingNumber = matchedArray.value.includes(randomeDigitNumber)
    if (!existingNumber && bingoMatrix.value.includes(randomeDigitNumber)) {
      matchedArray.value.push(randomeDigitNumber)
    }
  }, 1000)
}

function refreshTheGame() {
  clearInterval(interval.value)
  generateMatrixNumber()

  displayDigit.value = 0

  matchedArray.value = []

  wininingLineNumber.value = 0

  wonRows.value = []
  wonColumns.value = []
}

onMounted(generateMatrixNumber)
</script>

<style scoped>
.main-container {
  border: 2px solid #ff4f00;
  display: flex;
  flex-direction: column;
  margin-inline: 300px;
  box-shadow:
    0 2px 3px rgba(0, 0, 0, 0.1),
    0 2px 10px rgba(0, 0, 0, 0.15);
  border-radius: 5px;
  overflow: hidden;
}

header {
  text-align: center;
  background-color: #f8f9fa;
  font-family: 'Arial', sans-serif;
}

h1 {
  font-size: 3em;
  color: #333;
  animation: textColorChange 6s infinite;
}

#display-digit {
  display: flex;
  justify-content: center;
  padding: 10px 0px;
  font-size: xxx-large;
  color: red;
  font-family: fantasy;
}

@keyframes textColorChange {
  0% {
    color: #ff5733;
  }
  25% {
    color: #33c1ff;
  }
  50% {
    color: #9b59b6;
  }
  75% {
    color: #2ecc71;
  }
  100% {
    color: #f1c40f;
  }
}

main {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  padding: 0px 20px;
}

.item {
  background-color: #ecf0f1;
  border: 2px solid #ddd;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 1.5em;
  font-weight: bold;
  color: #333;
}

footer {
  display: flex;
  justify-content: center;
  margin-top: 3%;
  padding: 20px;
  background-color: #f1f1f1;
  border-top: 2px solid #ddd;
}

#start-btn,
#refresh-btn {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 15px 25px;
  font-size: 16px;
  cursor: pointer;
  border-radius: 10px;
  transition:
    background-color 0.3s,
    transform 0.2s;
}

#refresh-btn {
  background-color: #ff5722;
  margin-left: 10px;
}

#start-btn:hover,
#refresh-btn:hover {
  background-color: #45a049;
  transform: scale(1.05);
}

button:active {
  transform: scale(0.98);
}

.hightlight {
  background-color: #2ecc71;
  color: white;
}

.item.winningLine {
  background-color: gold !important;
  color: black;
  font-weight: bold;
  border: 2px solid #d4af37;
}

#display-win-match {
  background-color: #88e788;
  color: green;
  text-align: center;
  margin: 5px 0px;
  padding: 10px;
}
</style>
