<template>
  <div>
    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" />
    <!-- //since we cannot use v-for and v-if together we use template as parent element for the "v-if"  -->
    <template v-if="this.question">
      <h1>{{ this.question }}</h1>
      <template v-for="answer in answers" :key="answer">
        <input
          :disabled="this.answerSubmitted"
          type="radio"
          name="options"
          :value="answer"
          v-model="this.chosenAnswer"
        />
        <label v-html="answer"></label> <br />
      </template>
      <button
        v-if="!this.answerSubmitted"
        @click="this.submitAnswer()"
        class="send"
        type="button"
      >
        Send
      </button>

      <section class="result" v-if="this.answerSubmitted">
        <h4 v-if="this.chosenAnswer == this.correctAnswer">
          &#9989; Congratulations, You chose the right answer !"
        </h4>
        <h4 v-else>
          &#10060; You chose the wrong answer!! The correct answer is
        </h4>
        <button @click="getNewQuestion()" class="send" type="button">
          Next Question
        </button>
      </section>
    </template>
  </div>
</template>

<script>
import ScoreBoard from "./components/ScoreBoard.vue";
export default {
  name: "App",
  components: { ScoreBoard },
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0,
    };
  },

  computed: {
    answers() {
      //here we are trying to insert correct +incorrect ansers in one array at random positons
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(
        //it takes three args (position to insert, no. of elements to remove , element to insert )
        Math.round(Math.random() * this.incorrectAnswers.length), //1st agrs
        0, //2nd args
        this.correctAnswer //3rd args
      );
      return answers;
    },
  },
  created() {
    this.getNewQuestion();
  },
  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert("Please choose one option!");
      } else {
        this.answerSubmitted = true;
        if (this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.axios
        .get(
          "https://opentdb.com/api.php?amount=10&category=22&difficulty=easy&type=multiple"
        )
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
        });
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
}

#app input[type="radio"] {
  margin: 12px 4px;
}

#app button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: cadetblue;
  border: 1px solid cadetblue;
  cursor: pointer;
}
</style>
