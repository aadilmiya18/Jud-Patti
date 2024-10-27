<!-- <template>
    <div class="q-pa-md">
      <h2>Jud Patti Card Game</h2>
      <q-btn @click="shuffleDeck" label="Shuffle Deck" color="primary" />
      <q-btn @click="drawCards" label="Draw Cards" color="secondary" />

      <div v-if="cards.length > 0">
        <h3>Player Cards:</h3>
        <div class="q-gutter-md row">
          <div v-for="card in cards" :key="card.code" class="col-3">
            <q-img :src="card.image" :alt="card.code" />
          </div>
        </div>
      </div>
    </div>
  </template>

  <script>
  import axios from 'axios';

  export default {
    data() {
      return {
        deckId: null,
        cards: []
      };
    },
    methods: {
      async shuffleDeck() {
        try {
          const response = await axios.get('https://deckofcardsapi.com/api/deck/new/shuffle/');
          this.deckId = response.data.deck_id;
          this.cards = [];
        } catch (error) {
          console.error('Error shuffling deck:', error);
        }
      },
      async drawCards() {
        if (!this.deckId) {
          alert('Please shuffle the deck first.');
          return;
        }

        try {
          const response = await axios.get(`https://deckofcardsapi.com/api/deck/${this.deckId}/draw/?count=3`);
          this.cards = response.data.cards;
        } catch (error) {
          console.error('Error drawing cards:', error);
        }
      }
    }
  };
  </script>

  <style scoped>
  .q-img {
    max-width: 100%;
    height: auto;
  }
  </style> -->
