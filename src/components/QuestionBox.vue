<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">{{currentQuestion.question}}</template>
      <hr class="my-4" />
      <b-list-group>
        <b-list-group-item v-for="(answer, index) in answers" :key="index" @click="selectAnswer(index)" :class="[
                                  !answered && selectedIndex == index ? 'selected' : 
                                   answered &&  correctIndex == index ? 'correct' : 
                                   answered &&  correctIndex !== index &&selectedIndex == index ? 'incorrect' :''
                                   ]" >
            {{answer}}
        </b-list-group-item>
      </b-list-group>

      <b-button variant="primary" @click="submitAnswer" :disabled="selectedIndex === null || answered" >Submit</b-button>
      <b-button variant="success" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash';
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
        selectedIndex: null,
        correctIndex: null,
        shuffledAnswers: [],
        answered: false
    }
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      // answers.push(this.currentQuestion.correct_answers);
      return answers;
    }
  },
  watch: {
      currentQuestion: {
        immediate: true,
        handle() { 
          this.selectedIndex = null;
          this.answered = false;
          this.shuffleAnswers();
        }
      }
  },
  methods: {    
    selectAnswer(index) {
        this.selectedIndex = index;
    },
    
    submitAnswer() {
      let isCorrect = false;
      if(this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answers];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffleAnswers.indexOf(this.currentQuestion.correct_answers);
    },
    answerClass(index){
      let nameClass = '';
      if(!this.answered && this.selectedIndex == index)
        nameClass = 'selected';
      else if(this.answered &&  this.correctIndex == index)
         nameClass = 'correct';
      else if(this.answered &&  this.correctIndex !== index &&this.selectedIndex == index)
        nameClass = 'incorrect';
      return nameClass;                  
    }
  },
  mounted() {
    // console.log(this.currentQuestion);
    this.shuffleAnswers();
  }
};
</script>

<style scoped>
    .list-group {
        margin-bottom: 15px;
        
    }   
    .btn {
        margin: 0px 5px;
    }
    .list-group-item{
        cursor: pointer;
    }
    .list-group-item:hover {
        background: #EEE;
    }
    .selected {
        background-color: #99ddff;
        transition: 1s;
    }
    .correct { 
        background-color: green;
    }
    .incorrect {
        background-color: red;
    }
</style>