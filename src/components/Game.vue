<template>
  <div class="container">
    <section class="players d-flex my-4">
      <div class="player">
        <h4 class="player-name text-center">YOU</h4>
        <div class="health-bar">
          <p class="health">{{ playerHealth }}</p>
          <div class="health-remaining" :style="{ width: playerHealth + '%' }"></div>
          <div class="health-empty"></div>
        </div>
      </div>
      <div class="player">
        <h4 class="player-name text-center">MONSTER</h4>
        <div class="health-bar">
          <p class="health">{{ monsterHealth }}</p>
          <div class="health-remaining" :style="{ width: monsterHealth + '%' }"></div>
          <div class="health-empty"></div>
        </div>
      </div>
    </section>
    <div>
      <b-card class="mb-2 text-center d-flex" v-if="gameIsRunning">
        <b-button href="#" variant="danger" class="mr-2" @click="attack">Attack</b-button>
        <b-button
          href="#"
          variant="warning"
          class="mr-2"
          @click="specialAttack"
          v-show="attackCount >= 3"
        >Special Attack</b-button>
        <b-button
          href="#"
          variant="success"
          class="mr-2"
          @click="heal"
          v-show="playerHealth <= 95"
        >Heal</b-button>
        <b-button
          href="#"
          variant="primary"
          class="mr-2"
          @click="gameIsRunning = false; winner = 'monster'"
        >Give up</b-button>
      </b-card>
      <b-card class="mb-2 text-center d-flex" v-if="!gameIsRunning">
        <b-button href="#" variant="success" @click="restart">Start new Game</b-button>
      </b-card>
      <b-card class="mb-2 text-center d-flex" v-if="!gameIsRunning">
        <h2
          :style=" winner == 'player' ? 'border: 3px solid green;' : 'border: 3px solid red;' "
        >Winner is the {{ winner }}</h2>
      </b-card>
      <b-card class="mb-2 text-center d-flex">
        <div>
          <ul>
            <li v-for="(turn, index) in turns" :key="`item-${index}`">
              <h5
                :style=" turn.isPlayer ? 'border: 3px solid green;' : 'border: 3px solid red;' "
              >{{ turn.text }}</h5>
            </li>
          </ul>
        </div>
      </b-card>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      attackCount: 0,
      playerHealth: 100,
      monsterHealth: 100,
      turns: [],
      gameIsRunning: true,
      winner: ""
    };
  },
  methods: {
    heal() {
      let addToHealth = 0;
      this.attackCount = 0;

      if (this.playerHealth >= 90) {
        addToHealth = this.calculateHealth(5, 100 - this.playerHealth);
      } else {
        addToHealth = this.calculateHealth(5, 10);
      }

      this.playerHealth += addToHealth;

      this.turns.unshift({
        isPlayer: true,
        text: "Player heals for " + addToHealth
      });

      this.monsterAttack();
    },
    specialAttack() {
      let damage = 0;

      if (this.monsterHealth <= 20) {
        damage = this.calculateHealth(1, this.monsterHealth);
      } else {
        damage = this.calculateHealth(10, 20);
      }

      this.monsterHealth -= damage;
      this.attackCount = 0;
      this.attack();
    },
    attack() {
      let damage = 0;

      if (this.monsterHealth <= 10) {
        damage = this.calculateHealth(1, this.monsterHealth);
      } else {
        damage = this.calculateHealth(3, 10);
      }

      this.attackCount++;
      this.monsterHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        text: "Player hits Monster for " + damage
      });

      if (this.checkWin() == true) {
        return;
      }

      this.monsterAttack();
    },
    monsterAttack() {
      let damage = 0;

      if (this.playerHealth <= 10) {
        damage = this.calculateHealth(1, this.playerHealth);
      } else {
        damage = this.calculateHealth(3, 10);
      }

      this.playerHealth -= damage;
      this.turns.unshift({
        isPlayer: false,
        text: "Monster hits Player for " + damage
      });

      if (this.checkWin() == true) {
        return;
      }
    },
    calculateHealth(min, max) {
      return Math.floor(Math.random() * max) + min;
    },
    checkWin() {
      if (this.monsterHealth <= 0) {
        this.winner = "player";
        this.gameIsRunning = false;
        return true;
      } else if (this.playerHealth <= 0) {
        this.winner = "monster";
        this.gameIsRunning = false;
        return true;
      }
      return false;
    },
    restart() {
      (this.attackCount = 0),
        (this.playerHealth = 100),
        (this.monsterHealth = 100),
        (this.turns = []),
        (this.gameIsRunning = true),
        (this.winner = "");
    }
  }
};
</script>  justify-content: flex-start;

<style scoped>
.container {
  max-width: 900px;
  width: 90%;
}

.player {
  width: 50%;
}

.health {
  position: relative;
  z-index: 100;
  text-align: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.health-bar {
  position: relative;
  display: flex;
  margin: auto;
  width: 90%;
  height: 40px;
  background-color: grey;
}

.health-remaining {
  height: 40px;
  position: absolute;
  width: 100%;
  background-color: green;
}

.health-empty {
  height: 40px;
  background-color: transparent;
}
</style>