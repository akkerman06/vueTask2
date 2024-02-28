<template>
  <div id="app">
    <div class="colours">
      <div class="colours_one">
        <div class="red" id="1" @click="registerClick(1)"></div>
        <div class="blue" id="2" @click="registerClick(2)"></div>
      </div>
      <div class="colours_two">
          <div class="yellow" id="3" @click="registerClick(3)"></div>
          <div class="green" id="4" @click="registerClick(4)"></div>
      </div>
    </div>
    <div class="data">
      <div>
        <button @click="startGame()" class="btn btn-start">Start</button>
        <span>Раунд {{ this.round  }}</span>
      </div>
      <div class="options">
        <span>Уровни сложности</span>
        <div class="difficulty">
          <label for="1">Легкий</label>
          <input type="radio" value="1500" v-model="level">
        </div>
        <div class="difficulty">
          <label for="2">Средний</label>
          <input type="radio" value="1000" v-model="level">
        </div>
        <div class="difficulty">
          <label for="3">Сложный</label>
          <input type="radio" value="400" v-model="level">
        </div>        
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SimonGame',
  data(){
    return {
      level: '1500',
      round: 0,
      sequence: [],
      numOfClicks: -1,
      active: true,
    }
  },
  methods: {
    startGame() {
      this.round = 0
      this.sequence = []
      this.startRound()
    },
    startRound(){
      this.round += 1
      this.numOfClicks = -1
      this.active = true
      this.increaseSequence(this.generateSequence())
      this.animate()
    },
    increaseSequence(num) {
      this.sequence.push(num)
    },
    animate() {
      setTimeout(() => {
        let i = 0
        let interval = setInterval(() => {
          this.lightUp(this.sequence[i])
          this.playSound(this.sequence[i])
          ++i

          if(i >= this.sequence.length) {
            clearInterval(interval)
            this.active = false
          }
        } ,+this.level)
      }, 500)
    },
    generateSequence() {
      return Math.floor((Math.random() * 4) + 1)
    },
    lightUp(square) {
      const button = document.getElementById(square)
      button.classList.add('active')
      setTimeout(() => {
        button.classList.remove('active')
      },300)
    },
    playSound(square) {
      new Audio(`./sounds/${square}.ogg`).play()
    },
    registerClick(square) {
      if(this.active){
        return
      }

      this.numOfClicks++
      this.lightUp(square)
      this.playSound(square)

      if(square !== this.sequence[this.numOfClicks]){
        this.finishGame()
      }
      else if(this.sequence.length === this.numOfClicks + 1){
        this.startRound()
      }
    },
    saveBestScore() {
      const bestScore = localStorage.getItem('bestScore')
      if (!bestScore || this.round > bestScore) {
        localStorage.setItem('bestScore', this.round)
      }
    },

    finishGame() {
      this.active = true
      this.saveBestScore()

      setTimeout(() => {
        alert(`Вы програли на ${this.round} раунде. Лучший результат: ${localStorage.getItem('bestScore')}`)
      }, 600)
    }
  }
}
</script>

<style lang="sass">

  #app
    display: flex
    justify-content: center
    align-items: center
    gap: 30px
    margin-top: 30px

  .colours 
    display: flex
    flex-direction: column
    align-items: center

  .colours_one 
    display: flex

  .colours_one .red, .colours_one .blue 
    flex: 1

  .colours_two 
    display: flex
  .colours_two div 
    flex: 1
  .red, .blue, .yellow, .green
    width: 100px
    height: 100px
    border: 2px solid black
    border-radius: 2px
    opacity: 0.5
    cursor: pointer
    transition: all .1s ease

    &:hover
      border-color: gray
    &.active
      opacity: 1
  .red
    background-color: red
  .blue
    background-color: blue
  .yellow
    background-color: yellow  
  .green
    background-color: green 


               
</style>
