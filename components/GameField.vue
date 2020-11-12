<template>
  <div class="GameField">
    <client-only>
      <GameStatistic
        :step="stepNumber"
        :game-started-at="startedAt"
        :timer-in-progress="stepNumber > 0 && gameInProgress"
        :game-is-won="gameIsWon"
      />

      <transition-group
        name="GameCells"
        tag="div"
        class="GameField__container"
      >
        <GameCells
          v-for="num in cells" 
          :key="num"
          :number="num"
          :is-empty="num === 0"
          @click.native="moveCell(num)"
        />
      </transition-group>

      <div class="GameField__restartBlock">
        <button
          class="GameField__restartButton GameButton"
          @click="startNewGame"
        >
          Начать новую игру
        </button>
      </div>
    </client-only>
  </div>
</template>

<script>
import GameStatistic from '~/components/GameStatistic.vue'
import GameCells from '~/components/GameCells.vue'

// Инициализация ячеек игры
function getInitialCells () {
  const cells = Array.from({length: 15}, (cell, i) => i + 1)
  cells.push(0)

  return cells
}

export default {
  name: 'GameField',

  components: {
    GameStatistic,
    GameCells,
  },

  data: () => ({
    cells: getInitialCells(),
    stepNumber: 0,
    startedAt: Date.now(),
    gameInProgress: false,
    gameIsWon: false,
  }),

  // Вычисляемые свойства
  computed: {
    // Определение порядка ячеек
    initialCellsOrder () {
      return getInitialCells()
    },

    // Определение порядка строк из ячеек
    initialCellsOrderString () {
      return this.initialCellsOrder.join()
    },

    // Определение позиции пустой ячейки
    emptyCellPosition () {
      return this.cells.indexOf(0)
    },
  },

  // Слежение
  watch: {
    // Условие, при котором игра считается выигранной:
    // строка '1, 2, ... 15' === строке '1, 2, ... 15'
    cells (val) {
      if (val.join() === this.initialCellsOrderString) {
        this.completeTheGame()
      }
    },
    // Запуск счетчика игры
    stepNumber (val, oldVal) {
      if (oldVal === 0) {
        this.gameInProgress = true
      }
    },
    // Запуск времени игры
    gameInProgress (val) {
      if (!!val) {
        this.startedAt = Date.now()
      }
    },
  },

  created () {
    this.shuffleCells()
  },

  methods: {
    // Перемешивание ячеек
    shuffleCells () {
      this.cells.sort(() => Math.random() - 0.5)
    },

    // Инициализация новой игры
    startNewGame () {
      this.shuffleCells()
      this.stepNumber = 0
      this.startedAt = Date.now()
      this.gameInProgress = false
      this.gameIsWon = false
    },

    // Определение позиции ячейки
    getCellPosition (cell) {
      return this.cells.indexOf(cell)
    },

    // Завершение игры
    completeTheGame () {
      this.gameInProgress = false
      this.gameIsWon = true
      this.$emit('game-was-won')
    },

    // Опредение возможности двигать ячейку
    isMoveable (num) {
      const cellPosition = this.getCellPosition(num)
      const emptyCellPosition = this.emptyCellPosition
      const colsAmount = 4

      // перемещаемая ячейка и пустая должны находиться рядом (1 - слева-справа, colsAmount - друг над другом)
      return [1, colsAmount].includes(Math.abs(cellPosition - emptyCellPosition))
    },

    // Передвижение ячейки
    moveCell (num) {
      if (!this.isMoveable(num)) {
        return
      }
      const cellPos = this.getCellPosition(num)
      this.$set(this.cells, this.emptyCellPosition, num)
      this.$set(this.cells, cellPos, 0)
      this.stepNumber++
    },
  },
}
</script>

<style lang="scss">
  @import '~assets/styles/variables';

  .GameField {
    min-width: 300px;
    width: 100%;
    padding: 15px;
    border-radius: 10px;

    background-color: $gameFieldBg;

    &__container {
      display: grid;

      grid-template-areas: 'a a a a';
      grid-template-columns: repeat(4, 1fr);
      grid-template-rows: repeat(4, 1fr);
      grid-gap: 0.4rem;
    }

    &__restartBlock {
      margin-top: 1rem;
    }

    &__restartButton {
      display: block;
      width: 100%;
    }
  }

  /* Анимирование ячеек */
  .GameCells-move {
    transition: all .1s ease-in;
  }
</style>
