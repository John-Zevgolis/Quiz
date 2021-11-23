<template>
  <score-board :winCount="winCount" :loseCount="loseCount"></score-board>

  <section v-if="question">
      <h1 v-html="question"></h1>
      <div v-for="(answer, index) in answers" :key="index">
        <input 
          type="radio" 
          name="options" 
          :value="answer" 
          :id="`option-${index}`" 
          v-model="chosenAnswer" 
          :disabled="answerSubmitted"> 
        <label :for="`option-${index}`" v-html="answer"></label><br>
      </div>
      
      <button v-if="!answerSubmitted" class="send" type="button" @click="submitAnswer">Send</button>
  </section>

  <section class="result" v-if="answerSubmitted">
      <h4 v-if="chosenAnswer == correctAnswer" v-html="`&#9989; Congratulations, the answer ${correctAnswer} is correct.`"></h4>
      <h4 v-else v-html="`&#10060;  I'm sorry, you picked the wrong answer. The correct is ${correctAnswer}.`"></h4>
      <button class="send" type="button" @click="getNewQuestion">Next question</button>
  </section>
</template>

<script>
import ScoreBoard from './components/ScoreBoard.vue';

export default {
  components: {
    ScoreBoard
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    };
  },
  computed: { 
    answers() {
      let answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if(!this.chosenAnswer) {
        alert('Pick one of the options');
      } else {
        this.answerSubmitted = true;
        if(this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios('https://opentdb.com/api.php?amount=1&category=18')
      .then((res) => {
        this.question = res.data.results[0].question;
        this.incorrectAnswers = res.data.results[0].incorrect_answers;
        this.correctAnswer = res.data.results[0].correct_answer;
      })  
    }
  },
  created() {
    this.getNewQuestion();
  }
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

input[type='radio']{
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
}
</style>
