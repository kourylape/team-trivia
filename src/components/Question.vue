<template>
  <div id="questions" class="mb-10">
    <div id="flash" v-if="submitted">
      <p class="text-green" v-if="correct">CORRECT!</p>
      <p class="text-red" v-else>INCORRECT!</p>
    </div>
    <div v-else id="question">
      <p class="category"><strong>Category:</strong> {{ question.category }}</p>
      <h3 class="text" v-html="question.question" />
      <form class="qform" novalidate @submit="submitQuestion">
        <div v-if="question.type == 'boolean'">
          <label>
            <input type="radio" v-model="answer" value="True" class="radio" />
            <span>True</span>
          </label>
          <label>
            <input type="radio" v-model="answer" value="False" class="radio" />
            <span>False</span>
          </label>
        </div>

        <div v-if="question.type == 'multiple'">
          <div v-for="a in answers" :key="a">
            <label>
              <input
                type="radio"
                name="q"
                v-model="answer"
                v-bind:value="a"
                class="md-accent"
              />
              <span v-html="a" />
            </label>
          </div>
        </div>

        <div id="actions">
          <button
            type="submit"
            class="p-2 shadow-md rounded bg-purple text-dark hover:bg-dark hover:text-purple"
          >
            Submit Answer
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: "Categories",
  props: ["team", "trivia"],
  data() {
    return {
      players: "",
      answer: "",
      question: {},
      answers: [],
      submitted: false,
      correct: false,
    };
  },
  mounted() {
    this.buildQuestion();
  },
  methods: {
    buildQuestion: function () {
      if (this.trivia.questions.length > 0) {
        this.submitted = false;
        this.question = this.trivia.questions[0];
        this.answers = this.sortAnswers();
        this.answer = "";
      }
    },
    sortAnswers: function () {
      return this.question.incorrect_answers
        .concat(this.question.correct_answer)
        .sort();
    },
    submitQuestion: function (event) {
      event.preventDefault();
      if (this.answer !== "") {
        const correct = this.answer === this.question.correct_answer;
        this.correct = correct;
        this.submitted = true;
        setTimeout(() => {
          this.$emit("answer", correct);
          this.buildQuestion();
        }, 3000);
      }
    },
  },
};
</script>

<style scoped>
@reference "../assets/global.css";

#questions {
  #flash {
    font-size: 4em;
  }
  #question {
    text-align: left;
    .text {
      margin: 0.4em 0;
      font-size: 1.4em;
      line-height: 1.5em;
    }
    label {
      @apply block my-2;
      span {
        @apply ml-3;
      }
    }
    input[type="radio"] {
      transform: scale(3);
      @apply accent-purple ml-2;
    }
  }
  .qform {
    #actions {
      margin-top: 1em;
      text-align: center;
      .md-button {
        font-size: 0.6em;
        height: 2.5em;
      }
    }
  }
}
</style>
