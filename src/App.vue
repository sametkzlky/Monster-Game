<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns" style="width: 450px;">
        <h1 class="text-center">SEN</h1>
        <div class="healthbar">
          <div :style="playerProgress" class="healthbar text-center" style="background-color: green; margin: 0; color: white;">
            {{ playerHeal }}%
          </div>
        </div>
      </div>
      <div class="small-6 columns" style="width: 450px;">
        <h1 class="text-center">CANAVAR</h1>
        <div class="healthbar">
          <div :style="monsterProgress" class="healthbar text-center" style="background-color: green; margin: 0; color: white;">
            {{ monsterHeal }}%
          </div>
        </div>
      </div>
    </section>

    <section class="row controls" v-if="!gameIsOn">
      <div class="small-12 columns">
        <button id="start-game" @click="startGame">YENİ OYUN</button>
      </div>
    </section>

    <section class="row controls" v-if="gameIsOn">
      <div class="small-12 columns">
        <button id="attack" @click="attack">SALDIRI</button>
        <button id="special-attack" @click="specialAttack">ÖZEL SALDIRI</button>
        <button id="heal" @click="healUp">İLK YARDIM</button>
        <button id="give-up" @click="giveUp">PES ET!</button>
      </div>
    </section>

    <section class="row log" v-if="logs.length > 0">
      <div class="small-12 columns">
        <ul>
          <li
            :class="{ 'player-turn' : log.turn == 'p', 'monster-turn' : log.turn == 'm' }"
            v-for="(log, index) in logs" :key="index">{{ log.text }}</li>
        </ul>
      </div>
    </section>
   
  </div>
</template>

<script>
export default {
  data() {
    return {
      playerHeal: 100,
      monsterHeal: 100,
      logs: [],
      gameIsOn: false,
      attackMultiple: 10,
      specialAttackMultiple: 30,
      healUpMultiple: 20,
      monsterAttackMultiple: 25,
      logText: {
        attack: "OYUNCU ATAĞI :",
        specialAttack: "ÖZEL OYUNCU ATAĞI : ",
        monsterAttack: "CANAVAR ATAĞI : ",
        healUp: "İLK YARDIM : ",
        giveUp: "OYUNCU PES ETTİ!!!"
      }
    };
  },
  methods: {
    startGame() {
      this.gameIsOn = true;
    },
    attack() {
      const point = Math.ceil(Math.random() * this.attackMultiple);
      this.monsterHeal -= point;
      this.addToLog({ turn: "p", text: this.logText.attack + point });
      this.monsterAttack();
    },
    specialAttack() {
      const point = Math.ceil(Math.random() * this.specialAttackMultiple);
      this.monsterHeal -= point;
      this.addToLog({ turn: "p", text: this.logText.specialAttack + point });
      this.monsterAttack();
    },
    healUp() {
      const point = Math.ceil(Math.random() * this.healUpMultiple);
      this.playerHeal += point;
      this.addToLog({ turn: "p", text: this.logText.healUp + point });
      this.monsterAttack();
    },
    giveUp() {
      this.playerHeal = 0;
      this.addToLog({ turn: "p", text: this.logText.giveUp });
    },
    monsterAttack() {
      const point = Math.ceil(Math.random() * this.monsterAttackMultiple);
      this.playerHeal -= point;
      this.addToLog({ turn: "m", text: this.logText.monsterAttack + point });
    },
    addToLog(log) {
      this.logs.push(log);
    }
  },
  watch: {
    playerHeal(value) {
      if (value <= 0) {
        this.playerHeal = 0;
        if (confirm("Oyunu KAYBETTİN. Tekrar denemek ister misin?")) {
          this.playerHeal = 100;
          this.monsterHeal = 100;
          this.logs = [];
        }
      } else if (value >= 100) {
        this.playerHeal = 100;
      }
    },
    monsterHeal(value) {
      if (value <= 0) {
        this.monsterHeal = 0;
        if (confirm("Oyunu KAZANDIN. Tekrar denemek ister misin?")) {
          this.playerHeal = 100;
          this.monsterHeal = 100;
          this.logs = [];
        }
      }
    }
  },
  computed: {
    playerProgress() {
      return {
        width: this.playerHeal + "%"
      };
    },
    monsterProgress() {
      return {
        width: this.monsterHeal + "%"
      };
    }
  }
};
</script>

<style scoped>
.text-center {
  text-align: center;
  font-size: 2em;
  font-family: sans-serif;
}
.row{
  max-width: 62.5rem;
    margin-left: auto;
    margin-right: auto;
    display: flex;
    justify-content: space-around;
}
.healthbar {
  width: 100%;
  height: 40px;
  background-color: #eee;
  margin: auto;
  transition: width 500ms;
  font-size: 20px;
}
.controls,
.log {
  margin-top: 30px;
  text-align: center;
  padding: 10px;
  border: 1px solid #ccc;
  box-shadow: 0px 3px 6px #ccc;
}

.turn {
  margin-top: 20px;
  margin-bottom: 20px;
  font-weight: bold;
  font-size: 22px;
}
.small-12{
  width: 100%;
}

.log ul {
  list-style: none;
  font-weight: bold;
  text-transform: uppercase;
}

.log ul li {
  margin: 5px;
}

.log ul .player-turn {
  color: blue;
  background-color: #e4e8ff;
}

.log ul .monster-turn {
  color: red;
  background-color: #ffc0c1;
}

button {
  font-size: 20px;
  background-color: #eee;
  padding: 12px;
  box-shadow: 0 1px 1px black;
  cursor: pointer;
  margin: 10px;
  border: none;
}

#start-game {
  background-color: #aaffb0;
}

#start-game:hover {
  background-color: #76ff7e;
}

#attack {
  background-color: #ff7367;
}

#attack:hover {
  background-color: #ff3f43;
}

#special-attack {
  background-color: #ffaf4f;
}

#special-attack:hover {
  background-color: #ff9a2b;
}

#heal {
  background-color: #aaffb0;
}

#heal:hover {
  background-color: #76ff7e;
}

#give-up {
  background-color: #ffffff;
}

#give-up:hover {
  background-color: #c7c7c7;
}
</style>
