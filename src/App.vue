<template>
  <div class="flex flex-col h-full">
    <header>
      <h1 class="text-2xl text-purple">Team Trivia</h1>
    </header>

    <div v-if="trivia">
      <div id="game" class="container mx-auto p-2 text-dark flex-grow">
        <div class="flex justify-between">
          <span class="flex-1/4">
            <strong>Team:</strong> {{ activeTeam.name }}
          </span>
          <span class="flex-3/4">
            <strong>Players:</strong> {{ playerList }}
          </span>
        </div>

        <Question
          v-if="trivia.questions.length > 0"
          v-bind:team="team"
          v-bind:trivia="trivia"
          v-on:answer="answerQuestion"
        />

        <Categories
          v-else
          v-bind:team="team"
          v-on:update-questions="updateQuestions"
        />
      </div>

      <div
        class="bg-dark text-pink flex justify-between fixed bottom-0 w-full p-2"
      >
        <div class="flex-1/3">
          {{ this.trivia.teams[0].name }}:
          {{ this.trivia.teams[0].score }}
        </div>
        <div class="flex-1/3">
          <Debug v-bind:trivia="trivia" />
        </div>
        <div class="flex-1/3">
          {{ this.trivia.teams[1].name }}:
          {{ this.trivia.teams[1].score }}
        </div>
      </div>

      <Reset v-on:reset="resetGame" />
    </div>
    <div v-else class="container mx-auto">
      <NewGame v-on:start="startGame" />
    </div>
  </div>
</template>

<script>
import NewGame from "./components/NewGame.vue";
import Question from "./components/Question.vue";
import Categories from "./components/Categories.vue";
import Debug from "./components/Debug.vue";
import Reset from "./components/Reset.vue";

export default {
  name: "App",
  components: {
    NewGame,
    Question,
    Categories,
    Debug,
    Reset,
  },
  data() {
    return {
      trivia: this.trivia,
      team: {},
    };
  },
  mounted() {
    if (localStorage.trivia) {
      // if we already have trivia game, grab it from localstorage
      this.trivia = JSON.parse(localStorage.trivia);
    } else {
      this.trivia = false;
    }
  },
  computed: {
    activeTeam: function () {
      const roundNumber = this.trivia.round;
      let team = roundNumber % 2;
      if (this.trivia.questions.length > 0) {
        team = team == 0 ? 1 : 0;
      }
      return this.trivia.teams[team];
    },
    playerList: function () {
      return this.activeTeam.players
        .map((p) => {
          return p.name;
        })
        .join(", ");
    },
  },
  methods: {
    startGame(game) {
      this.trivia = game;
      localStorage.trivia = JSON.stringify(game);
    },
    resetGame() {
      this.trivia = false;
      delete localStorage.trivia;
    },
    updateQuestions(questions) {
      this.trivia.questions = questions;
      localStorage.trivia = JSON.stringify(this.trivia);
    },
    answerQuestion(correct) {
      if (correct) {
        this.activeTeam.score += 100;
      } else {
        this.activeTeam.score -= 50;
      }
      this.trivia.questions.shift();
      this.trivia.question += 1;

      if (this.trivia.questions.length <= 0) {
        this.trivia.question = 1;
        this.trivia.round += 1;
      }

      localStorage.trivia = JSON.stringify(this.trivia);
    },
  },
};
</script>

<style scoped>
@reference "./assets/global.css";

header {
  @apply bg-dark text-white shadow-lg py-2;
}
</style>
