<template>
  <div id="new-game" class="my-4">
    <h2 class="my-4 text-2xl">New Game</h2>

    <form novalidate @submit="createGame">
      <div class="flex gap-4">
        <!-- TEAM ONE -->
        <div class="flex-2/5 text-center w-full">
          <div class="mb-6">
            <input
              type="text"
              v-model="game.teams[0].name"
              placeholder="Team 1 Name"
            />
          </div>

          <h3>Players:</h3>

          <fieldset v-for="player in game.teams[0].players" :key="player.id">
            <input
              type="text"
              v-model="player.name"
              placeholder="Player Name"
            />
            <button
              class="delete-player-button"
              v-on:click.prevent="delPlayer(0, player.id)"
            >
              <span class="material-symbols-outlined">delete_forever</span>
            </button>
          </fieldset>
        </div>

        <!-- Actions -->
        <div class="flex-1/5 text-center w-full">
          <h3>VS</h3>
          <button
            class="my-6 p-2 bg-dark text-white rounded shadow-md text-xs"
            v-on:click.prevent="addPlayers"
          >
            Add Players
          </button>
        </div>

        <!-- TEAM TWO -->
        <div class="flex-2/5 text-center w-full">
          <div class="mb-6">
            <input
              type="text"
              v-model="game.teams[1].name"
              placeholder="Team 2 Name"
            />
          </div>

          <h3>Players:</h3>

          <fieldset v-for="player in game.teams[1].players" :key="player.id">
            <input
              type="text"
              v-model="player.name"
              placeholder="Player Name"
            />
            <button
              class="delete-player-button"
              v-on:click.prevent="delPlayer(1, player.id)"
            >
              <span class="material-symbols-outlined">delete_forever</span>
            </button>
          </fieldset>
        </div>
      </div>

      <button
        type="submit"
        class="submit-button my-6 p-2 bg-green rounded shadow-md"
      >
        Begin Game!
      </button>
    </form>
  </div>
</template>

<script>
import { v4 as uuidv4 } from "uuid";
export default {
  name: "NewGame",
  data() {
    return {
      game: {
        round: 1,
        question: 1,
        questions: [],
        teams: [
          {
            name: "",
            players: [],
            score: 0,
          },
          {
            name: "",
            players: [],
            score: 0,
          },
        ],
      },
    };
  },
  methods: {
    createGame: function (event) {
      event.preventDefault();
      this.$emit("start", this.game);
    },
    addPlayers: function () {
      for (let i = 0; i < 2; i++) {
        this.game.teams[i].players.push({
          id: uuidv4(),
          name: "",
        });
      }
    },
    delPlayer: function (team, playerId) {
      this.game.teams[team].players = this.game.teams[team].players.filter(
        (obj) => {
          return obj.id !== playerId;
        },
      );
    },
  },
};
</script>

<style scoped>
@reference "../assets/global.css";

#new-game {
  input[type="text"] {
    @apply p-0.5 border-b-2 border-blue outline-0 hover:border-cyan focus:border-cyan;
  }
  h3 {
    @apply text-sm;
  }
  fieldset {
    @apply my-2;
    input[type="text"] {
      @apply text-xs;
    }
  }
  .add-players {
    @apply my-4;
    font-size: 0.5rem;
  }
  .delete-player-button {
    @apply hover:text-red;
    span {
      @apply text-xs;
    }
  }
}
</style>
