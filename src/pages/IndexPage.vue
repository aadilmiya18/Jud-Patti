<template>
  <q-page>
    <div class="game-container">
      <div class="player-section" id="player-1">
        <h4>Player 1</h4>
        <div class="cards">
          <img
            v-for="card in players[0]?.cards"
            :src="card.image"
            :key="card.code"
            class="card-image"
          />
        </div>
      </div>
      <div class="center-area">
        <q-btn label="Shuffle Cards" color="primary" @click="startGame" />
        <div class="q-pa-md q-gutter-sm">
          <q-btn
            v-if="players.length && !winner"
            label="Declare Winner"
            color="secondary"
            @click="
              declareWinner();
              icon = true;
            "
          />
          <q-dialog v-model="icon">
            <q-card>
              <q-card-section class="row items-center q-pb-none">
                <div class="text-h6">The Winner is...</div>
                <q-space />
                <q-btn icon="close" flat round dense v-close-popup />
              </q-card-section>
              <q-card-section v-if="winner">
                {{ winner }}
              </q-card-section>
            </q-card>
          </q-dialog>
        </div>
      </div>
      <div class="player-section" id="player-2">
        <h4>Player 2</h4>
        <div class="cards">
          <img
            v-for="card in players[1]?.cards"
            :src="card.image"
            :key="card.code"
            class="card-image"
          />
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const icon = ref(false);
const deckId = ref("");
const players = ref([]);
const winner = ref(null);

const startGame = async () => {
  const { data } = await axios.get(
    "https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1"
  );
  deckId.value = data.deck_id;
  players.value = [];
  for (let i = 0; i < 2; i++) {
    const response = await axios.get(
      `https://deckofcardsapi.com/api/deck/${deckId.value}/draw/?count=3`
    );
    players.value.push({
      id: i + 1,
      cards: response.data.cards,
    });
  }
  winner.value = null;
};

const declareWinner = () => {
  const playerHands = players.value.map((player) => ({
    id: player.id,
    rank: rankHand(player.cards),
    cards: player.cards,
  }));
  playerHands.sort((a, b) => b.rank - a.rank);
  const bestHand = playerHands[0];
  winner.value = `Player ${bestHand.id} wins `;
};

const getCardValue = (value) => {
  if (value === "ACE") return 14;
  if (value === "KING") return 13;
  if (value === "QUEEN") return 12;
  if (value === "JACK") return 11;
  return parseInt(value);
};

const rankHand = (cards) => {
  const cardValues = cards
    .map((card) => getCardValue(card.value))
    .sort((a, b) => b - a);

  if (cardValues[0] === cardValues[1] && cardValues[1] === cardValues[2]) {
    return 10;
  }

  const isSequence =
    cardValues[0] - cardValues[1] === 1 && cardValues[1] - cardValues[2] === 1;
  const isFlush = cards.every((card) => card.suit === cards[0].suit);

  if (isSequence && isFlush) {
    return 9;
  }

  if (isSequence) {
    return 8;
  }

  if (isFlush) {
    return 7;
  }

  if (cardValues[0] === cardValues[1] || cardValues[1] === cardValues[2]) {
    return 6;
  }

  return Math.max(...cardValues);
};


</script>

<style scoped>
.game-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100vh;
  padding: 20px;
  background: radial-gradient(circle, #2b2b2b, #000);
  color: white;
}

.player-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  width: 30%;
}

.center-area {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.7);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.5);
  width: 30%;
}

.cards {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.card-image {
  width: 100px;
  height: 140px;
  border-radius: 8px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}
</style>
