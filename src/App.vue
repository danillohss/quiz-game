<template>
  <div class="app">
    <div v-if="this.question" class="question">
      <h1 v-html="this.question"></h1>
      <template v-for="(answer, index) in this.answers" :key="index">
        <input v-model="this.chosen_answer" type="radio" name="options" :value="answer" :disabled="this.answerSubmitted">
        <label v-html="answer"></label><br />
      </template>
      <button v-if="!this.answerSubmitted" class="send" @click="submitAnswer">Send</button><br />

      <section class="result" v-if="this.result">
        <h4 v-if="this.chosen_answer != this.correctAnswer"> Incorrect answer &#10060;</h4>
        <h4 v-else> Congratulations correct answer &#9989;</h4>
        <button @click="getNewQuestion" class="send">Next Question</button>
      </section>

    </div>
  </div>
</template>
<script>

export default {
  name: 'App',
  data() {
    return {
      question: null,
      incorrectAnswers: [],
      correctAnswer: null,
      chosen_answer: null,
      answerSubmitted: false,
      result: false,
    }
  },
  methods: {
    async getNewQuestion() {
      await this.axios.get('https://opentdb.com/api.php?amount=1&category=18')
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
          this.reset();
        })
    },
    submitAnswer() {
      this.result = true;
      if (!this.chosen_answer) {
        alert('Choose one option!')
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer == this.correctAnswer) {
          console.log('Correct answer');
        } else {
          console.log('Incorrect answer')
        }
      }
    },
    reset() {
      this.result = false;
      this.answerSubmitted = false;
      this.chosen_answer = null;
    },
  },
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    },
  },
  created() {
    this.getNewQuestion();
  },

}
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

h1 {
  margin-top: 40px;
}

input[type='radio'] {
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
  border-radius: 5px;
  transition: all 0.5s;
}

button.send:hover {
  background-color: #8608ec;
}

section.score {
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;

  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid black;
  }
}
</style>
