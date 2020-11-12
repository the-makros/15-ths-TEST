<template>
  <div class="GameContainer">
    <div
      v-if="displayStartDisplay"
      class="GameMessage"
    >
      <div
        v-if="displayStartDisplay"
        class="GameMessage__init"
      >
        <div class="GameMessage__text">
          <p>
            Добро пожаловать в игру "Пятнашки"
          </p>
        </div>
      </div>

      <div
        v-if="displayStartDisplay"
        class="GameMessage__init"
      >
        <button
          class="GameMessage__button GameButton"
          @click="displayStartDisplay = false"
        >
          Начать игру
        </button>
      </div>
    </div>

    <div
      v-else
    >
      <GameField />
    </div>
  </div>
</template>

<script>
import GameField from '~/components/GameField.vue'

export default {
  data: () => ({
    displayStartDisplay: true
  }),
}
</script>

<style lang="scss">
  @import '~assets/styles/game';

.GameContainer {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  background: url("../assets/img/back.jpg") 50% 50% no-repeat;
  background-size: cover;
}

.GameMessage {
  width: 30%;
  min-width: 400px;
  font-family: $fontSecondary;
  font-size: $fontSizeBase;

  &__init {
    margin: 20px;
  }

  &__text {
    padding: 15px;

  }

  @media screen and (min-width: 800px) {
    margin: 0 10%;
  }

  &__button {
    display: block;
    width: 100%;
    border-radius: 5px;
  }
}  
</style>