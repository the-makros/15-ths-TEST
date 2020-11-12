<template>
  <div class="GameStatistic">
    <div
      v-if="gameIsWon"
      class="GameStatistic__victory"
    >
      Вы выиграли!
    </div>
    <template v-else>
      <div class="GameStatistic__stepsAmount">
        <div class="GameStatistic__stepsAmount_title">
          Ход
        </div>
        <div class="GameStatistic__stepsAmount_content">
          {{ step }}
        </div>
      </div>
      <div class="GameStatistic__timer">
        <div class="GameStatistic__timer_title">
          Время
        </div>
        <div class="GameStatistic__timer_content">
          {{ timeElapsedReadable }}
        </div>
      </div>
    </template>

  </div>
</template>

<script>
export default {
  name: 'GameStatistic',

  // Получение данных из компонента GameField
  props: {
    // Информация о шагах
    step: {
      required: true,
      type: Number,
    },
    // Информация о времени начала игры
    gameStartedAt: {
      required: true,
      type: Number,
    },
    timerInProgress: Boolean,
    gameIsWon: Boolean,
  },

  data: () => ({
    interval: null,
    secondsElapsed: 0,
  }),

  // Вычисляемые свойства
  computed: {
    // Функция для отображения минут и секунд
    timeElapsedReadable () {
      let minutes = Math.floor(this.secondsElapsed / 60)
      let seconds = Math.floor(this.secondsElapsed % 60)

      // Функция padStart заполняет минуты и секунды, как 00:00
      return `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`
    },
  },

  // Слежение
  watch: {
    // Функция отслеживания времени игры
    gameStartedAt () {
      this.secondsElapsed = (Date.now() - this.gameStartedAt) / 1000
    },
  },

  // Инициализация таймера
  created () {
    this.startTimer()
  },

  // Остановка таймера перед началом новой игры
  beforeDestroy () {
    this.stopTimer()
  },

  methods: {
    startTimer () {
      this.stopTimer()
      this.interval = setInterval(() => {
        if (!this.timerInProgress) {
          return
        }
        this.secondsElapsed = (Date.now() - this.gameStartedAt) / 1000
      }, 300)
    },
    stopTimer () {
      if (this.interval !== null) {
        clearInterval(this.interval)
        this.interval = null
      }
    },
  },
}
</script>

<style lang="scss">
  @import '~assets/styles/variables';

  $bgColor: $gameTileBg;

  .GameStatistic {
    display: flex;
    margin-bottom: 1rem;

    &__stepsAmount, &__timer, &__victory {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 0.4rem;

      font-family: $fontPrimary;
      font-size: 1.4rem;
      background: $bgColor;
    }

    &__stepsAmount, &__timer {
      width: 50%;
    }
    &__victory {
      width: 100%;
    }
  }
</style>
