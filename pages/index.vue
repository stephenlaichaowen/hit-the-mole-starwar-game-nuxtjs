<template>
  <div class="wrapper">
    <div class="score">{{ score }}</div>
    <div class="countdown">{{ countdown }}</div>
    <button
      class="startButton"
      ref="startButton"
      :class="{ disabled: disabled }"
      @click="startGame"
    >{{ btnText }}</button>
    <div class="game">
      <div v-for="(hole, index) in holes" :key="index" ref="holes" :class="`hole hole${index}`">
        <div class="mole small" :style="[ isHit ? { backgroundImage: 'url(' + imageAfter + ')', pointerEvents: 'none' } : { backgroundImage: 'url(' + imageBefore + ')', pointerEvents: 'all' }]" @click.stop="whack"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  layout: 'starwargame',
  data() {
    return {
      lastHole: null,
      timeUp: false,
      timeLimit: 20000,
      score: 0,
      countdown: null,
      isGameStarted: false,
      holes: [1, 1, 1, 1, 1, 1],
      disabled: false,
      btnText: 'start',
      isHit: false,
      imageBefore: 'http://frankslaboratory.co.uk/wp-content/uploads/2020/08/yoda1.png',
      imageAfter: 'http://frankslaboratory.co.uk/wp-content/uploads/2020/08/yoda2.png'
    }
  },
  methods: {
    startGame() {
      this.disabled = true
      this.$refs.startButton.classList.add('disabled')
      this.btnText = 'hit the mole !'

      let countdown = this.timeLimit / 1000
      this.score = 0
      this.countdown = countdown
      this.timeUp = false
      this.score = 0
      this.popOut()

      setTimeout(() => {
        this.timeUp = true
      }, this.timeLimit)

      let startCountdown = setInterval(() => {
        countdown -= 1
        this.countdown = countdown
        if (countdown < 0) {
          countdown = 0
          clearInterval(startCountdown)
          this.countdown = 'Times UP!! Thank you for protecting our planet! This is the way!'
          this.disabled = false
          this.$refs.startButton.classList.remove('disabled')
          this.btnText = 'start'
        }
      }, 1000)
    },
    popOut() {
      const time = Math.random() * 1300 + 400
      const hole = this.pickRandomHole(this.$refs.holes)
      hole.classList.add('up')
      setTimeout(() => {
        hole.classList.remove('up')
        if (!this.timeUp) this.popOut()
      }, time)
    },
    pickRandomHole(holes) {
      const randomHole = Math.floor(Math.random() * holes.length)
      const hole = holes[randomHole]
      if (hole === this.lastHole) {
        return this.pickRandomHole(holes)
      }
      this.lastHole = hole
      return hole
    },
    whack() {
      this.score++
      this.isHit = true
      setTimeout(() => {
        this.isHit = false
      }, 300)
    }
  }
}
</script>

<style scoped>
.wrapper {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border: 1px solid #000;
  width: 800px;
  height: 600px;
  background-image: url(http://frankslaboratory.co.uk/wp-content/uploads/2020/08/background.png);
}

.score {
  font-size: 150px;
  color: var(--main-color);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  /* border: 1px solid #000; */
  width: 200px;
  height: 100px;
  text-align: center;
}

.countdown {
  position: absolute;
  top: 490px;
  width: 100%;
  font-size: 20px;
  text-align: center;
  color: var(--main-color);
}

button {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  background: var(--main-color);
  color: #fff;
  padding: 20px 50px;
  border-radius: 5px;
  z-index: 200;
  cursor: pointer;
  border: none;
  outline: none;
}

.game {
  /* border: 2px solid #000; */
  width: 600px;
  height: 400px;
  margin: 70px auto;
  z-index: 200;
  display: flex;
  flex-wrap: wrap;
}

.hole {
  /* border: 3px solid red; */
  flex: 1 0 33.33%;
  position: relative;
  overflow: hidden;
}

.hole::after {
  content: '';
  display: block;
  background-image: url(http://frankslaboratory.co.uk/wp-content/uploads/2020/08/sand1.png);
  position: absolute;
  width: 100%;
  height: 70px;
  z-index: 2;
  bottom: -30px;
  background-size: contain;
}

.mole {
  background-image: url(http://frankslaboratory.co.uk/wp-content/uploads/2020/08/yoda1.png);
  position: absolute;

  /* we can control the height of yoda by adjsusting top property */
  /* 30% - fully hidden */
  /* -20% - fully showed-up */
  top: 30%;
  /****************************************************************/

  width: 100%;
  height: 100%;
  transition: 0.4s;
  background-repeat: no-repeat;
  background-position: bottom;
}

.mole.small {
  background-size: 60%;
}

.mole.large {
  background-size: 80%;
}

.disabled {
  display: none;
}

.hole.up .mole {
  top: 0;
}
</style>