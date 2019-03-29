<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead" v-html="question.question"></template>

      <hr class="my-4">
      <b-list-group>
        <b-list-group-item
          :class="answerClass(index)"
          @click="selectAnswer(index)"
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
        >{{answer}}</b-list-group-item>
      </b-list-group>

      <b-button :disabled="selectedIndex === null || answered" variant="primary" @click="submitAnswer">Submit</b-button>
      <b-button @click="next" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    question: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.question.incorrect_answers];
      answers.push(this.question.correct_answer);
      return answers;
    }
  },
  watch: {
    question: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
        this.answered = false;
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.question.incorrect_answers,
        this.question.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(this.question.correct_answer)
    },
    submitAnswer(){
      let isCorrect = false
      if(this.selectedIndex === this.correctIndex){
        isCorrect = true
      }
      this.answered = true

      this.increment(isCorrect)
    },
    answerClass(index){
      let answerClass = ''
      if (!this.answered && this.selectedIndex === index){
        answerClass = 'selected'
      }else if(this.answered && this.correctIndex === index){
        answerClass = 'correct'
      }else if (this.answered && this.selectedIndex === index && this.correctIndex !== index){
        answerClass = 'incorrect'
      }
      return answerClass
    }
  }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background-color: #eee;
  cursor: pointer;
}
.btn {
  margin: 0px 5px;
}
.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: red;
}
</style>