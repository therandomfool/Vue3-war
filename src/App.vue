<template>
  <h1> War Game! </h1>
  <div v-if="!gameOver" id="game">
    <div id="playerOne">
      <h4>Player One</h4>
      <div class="card">
        <img :src="cardOne?.images?.png" alt="">
      </div>
    </div>
    <div id="playerTwo">
      <h4>Player Two</h4>
      <div class="card">
        <img :src="cardTwo?.images?.png" alt="">
      </div>

      <div id="scoreboard">
        <button @click = "getCards()">Draw Cards </button>
        <h4>Player One: {{playerOneScore}}  Player Two: {{playerTwoScore}}</h4>
      </div>
    </div>
  </div>
  <div v-if="gameOver">
    <button @click="getDeck()">Play Game</button>
  </div>
</template>

<script>
import {ref} from "vue";
import axios from "axios";

function translateCards(value) {
  switch (value) {
    case "JACK":
      value = '11';
      break;
    case "QUEEN":
      value = '12';
      break;
    case "KING":
      value = '13';
      break;
    case "ACE":
      value = '14';
      break;
    default:
      break;
  }
  return value;
}
export default {
  name: 'App',
  setup(){
    let gameOver=ref(true), 
    cardOne = ref({}), 
    cardTwo = ref({}), 
    deckId = ref(null),
    playerOneScore = ref(0), 
    playerTwoScore = ref(0);
    async function getDeck() {
    gameOver.value = false;
    if(deckId.value == null) {
      const { data } = await axios.get("https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1");
      deckId.value = data.deck_id;
    }
  }
  async function getCards() {
    const { data } = await axios.get(
      "https://deckofcardsapi.com/api/deck/" + deckId.value + "/draw/?count=2"
    )
    const remaining = data.remaining;
    const { cards } = data;
    cardOne.value = cards[0];
    cardTwo.value = cards[1];
    const valueOne = parseInt(translateCards(cardOne.value.value));
    const valueTwo = parseInt(translateCards(cardTwo.value.value));
    if (valueOne > valueTwo) playerOneScore.value += 1;
    if (valueOne < valueTwo) playerTwoScore.value += 1;

    if(remaining <=1 ) {
      gameOver.value = true;
      playerOneScore.value = 0;
      playerTwoScore.value = 0;
    }
  }
  
  return { getCards, cardOne, cardTwo, playerOneScore, playerTwoScore, gameOver, getDeck}
  },

 
  
};
</script>

<style>

</style>
