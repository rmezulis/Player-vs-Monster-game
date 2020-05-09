<template>
  <div class="container">
    <section class="players d-flex my-4">
      <div class="player">
        <h4 class="player-name text-center">YOU</h4>
        <b-progress
          height="40px"
          :value="playerHealth"
          show-progress
          class="health mb-2"
          variant="success"
        ></b-progress>
      </div>
      <div class="player">
        <h4 class="player-name text-center">Monster</h4>
        <b-progress
          height="40px"
          :value="monsterHealth"
          show-progress
          class="health mb-2"
          variant="success"
        ></b-progress>
      </div>
    </section>
    <b-card class="mb-2 text-center d-flex">
      <b-container>
        <b-button
          variant="danger"
          class="mr-2"
          @click="attack(); monsterAttack()"
          v-if="gameIsRunning"
        >Attack</b-button>
        <b-button
          variant="warning"
          class="mr-2"
          @click="specialAttack(); monsterAttack()"
          v-if="gameIsRunning"
        >Special Attack</b-button>
        <b-button
          variant="success"
          class="mr-2"
          @click="heal(); monsterAttack()"
          v-if="gameIsRunning"
        >Heal</b-button>
        <b-button
          variant="light"
          class="mr-2"
          v-if="gameIsRunning"
          @click="resetGame(false)"
        >Give Up</b-button>
        <b-button
          variant="success"
          @click="gameIsRunning = true"
          v-if="!gameIsRunning"
        >Start new game</b-button>
      </b-container>
    </b-card>
    <b-card class="mb-2 text-center d-flex">
      <div class="turns" v-for="turn in turns" :key="turn.id">
        <ul>
          <li class="turn you" v-if="turn.player === 'you'">{{turn.message}}</li>
          <li class="turn monster" v-if="turn.player === 'monster'">{{turn.message}}</li>
        </ul>
      </div>
    </b-card>
    <div>
      <b-modal id="playerWins" size="sm">
        <template v-slot:modal-header="{  }"></template>
        <p class="my-4">You won! New game?</p>
        <template v-slot:modal-footer="{ ok, cancel }">
          <b-button size="sm" variant="primary" @click="resetGame(true); ok()">Ok</b-button>
          <b-button size="sm" variant="primary" @click="cancel()">Cancel</b-button>
        </template>
      </b-modal>
      <b-modal id="monsterWins" size="sm">
        <template v-slot:modal-header="{  }"></template>
        <p class="my-4">You lost! New game?</p>
        <template v-slot:modal-footer="{ ok, cancel }">
          <b-button size="sm" variant="primary" @click="resetGame(true); ok()">Ok</b-button>
          <b-button size="sm" variant="primary" @click="cancel()">Cancel</b-button>
        </template>
      </b-modal>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      turns: [],
      turnCount: 0,
      gameIsRunning: false
    };
  },
  methods: {
    attack() {
      let damage = Math.floor(Math.random() * 5) + 1;
      this.monsterHealth -= damage;
      this.turns.unshift({
        id: this.turnCount++,
        message: `Player hits monster for ${damage}`,
        player: "you"
      });
    },
    monsterAttack() {
      let damage = Math.floor(Math.random() * 5) + 6;
      this.playerHealth -= damage;
      this.turns.unshift({
        id: this.turnCount++,
        message: `Monster hits player for ${damage}`,
        player: "monster"
      });
    },
    specialAttack() {
      let damage = Math.floor(Math.random() * 5) + 6;
      this.monsterHealth -= damage;
      this.turns.unshift({
        id: this.turnCount++,
        message: `Player hits monster hard for ${damage}`,
        player: "you"
      });
    },
    heal() {
      this.playerHealth += 10;
      this.turns.unshift({
        id: this.turnCount++,
        message: "Player heals for 10",
        player: "you"
      });
    },
    resetGame(status) {
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.turns = [];
      this.turnCount = 0;
      this.gameIsRunning = status;
    },
    showModal(id) {
      this.$bvModal.show(id);
    }
  },
  watch: {
    playerHealth: function() {
      if (this.playerHealth <= 0) {
        this.showModal("monsterWins");
      }
    },
    monsterHealth: function() {
      if (this.monsterHealth <= 0) {
        this.showModal("playerWins");
      }
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 900px;
  width: 90%;
}

.player {
  width: 50%;
}

.health {
  width: 90%;
  margin: auto;
}

.turns {
  padding: 0;
}

.turn {
  font-weight: bold;
  list-style-type: none;
  font-size: 12px;
  margin: -12px auto;
}

.you {
  color: blue;
  background-color: rgba(0, 0, 255, 0.2);
}

.monster {
  color: red;
  background-color: rgba(255, 0, 0, 0.2);
}
</style>
