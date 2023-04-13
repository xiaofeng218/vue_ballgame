<template>
  <div class="ball-game" :style="{ backgroundImage: 'url(dog.jpg)' }">
    <div
      class="ball"
      :style="{ top: ballPosition.y + 'px', left: ballPosition.x + 'px' }"
    ></div>
    <div
      class="paddle"
      :style="{ top: paddlePosition.y + 'px', left: paddlePosition.x + 'px', width: paddleWidth + 'px' }"
      ref="paddle"
    ></div>
    <button class="game-button" @click="startGame">{{ gameButtonLabel }}</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      ballPosition: { x: 0, y: 0 },
      ballVelocity: { x: 5, y: 5 },
      paddlePosition: { x: 0, y: 380 },
      paddleWidth: 80,
      gameInterval: null,
      gameOver: false,
      gameStarted: false,
    };
  },
  computed: {
    gameButtonLabel() {
      return this.gameOver ? "重新开始" : this.gameStarted ? "结束游戏" : "开始游戏";
    },
  },
  mounted() {
    this.initBallPosition();
    window.addEventListener("keydown", this.handleKeyDown);
    window.addEventListener("keyup", this.handleKeyUp);
  },
  methods: {
    startGame() {
      if (this.gameOver) {
        this.initBallPosition();
        this.gameOver = false;
      }
      this.gameStarted = !this.gameStarted;
      if (this.gameStarted) {
        this.gameInterval = setInterval(() => {
          this.updateGameState();
        }, 16);
      } else {
        clearInterval(this.gameInterval);
      }
    },
    updateGameState() {
      // Update ball position and check for collisions
      this.ballPosition.x += this.ballVelocity.x;
      this.ballPosition.y += this.ballVelocity.y;
      if (this.ballPosition.x < 0 || this.ballPosition.x > 400) {
        this.ballVelocity.x *= -1;
      }
      if (this.ballPosition.y < 0) {
        this.ballVelocity.y *= -1;
      }
      if (this.ballPosition.y > 400) {
        this.gameOver = true;
        this.gameStarted = false;
        clearInterval(this.gameInterval);
      }

      // Check for paddle collision
      const paddleRect = this.$refs.paddle.getBoundingClientRect();
      const ballRect = document.querySelector(".ball").getBoundingClientRect();
      if (
        ballRect.bottom >= paddleRect.top &&
        ballRect.right >= paddleRect.left &&
        ballRect.left <= paddleRect.right
      ) {
        this.ballVelocity.y *= -1;
      }
    },
    handleKeyDown(event) {
      if (event.key === "ArrowUp" && this.paddlePosition.y > 0) {
        this.paddlePosition.y -= 20;
      }
      if (event.key === "ArrowDown" && this.paddlePosition.y < 380) {
        this.paddlePosition.y += 20;
      }
      if (event.key === "ArrowLeft" && this.paddlePosition.x > 0) {
        this.paddlePosition.x -= 30;
      }
      if (event.key === "ArrowRight" && this.paddlePosition.x < 320) {
        this.paddlePosition.x += 30;
      }
    },
    handleKeyUp(event) {
      // Left blank intentionally
    },
    initBallPosition() {
      this.ballPosition.x = Math.floor(Math.random() * 300);
      this.ballPosition.y = 50;
    },
  },
};
</script>

<style>
.ball-game {
  position: relative;
  width: 400px;
  height: 400px;
  background-color: #eee;
  background-size: cover;
}

.ball {
  position: absolute;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: red;
}

.paddle {
  position: absolute;
  height: 10px;
  background-color: blue;
}

.game-button {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  padding: 8px 16px;
  font-size: 16px;
  background-color: #fff;
  border: 2px solid #333;
  border-radius: 4px;
  cursor: pointer;
}
</style>
