<template>
<!--Proyecto de quiz en 10 preguntas acerca de artistas, usando API -->
  <div id="app">
      <Header
        :numCorrect="numCorrect"
        :numTotal="numTotal"
      />
      <b-container class="bv-example-row">
        <b-row>
          <b-col sm=6 offset=3>
            <QuestionBox
              v-if="questions.length"
              :currentQuestion="questions[index]"
              :next="next"
              :increment="increment"
            />
          </b-col>
        </b-row>
      </b-container>
  </div>
</template>
<script>
//  imports
import Header from './components/Header.vue'
import QuestionBox from './components/QuestionBox.vue'
export default {
  name: 'App',
  components: {
    Header,
    QuestionBox
  },
  data () {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 1
    }
  },
  methods: {
    next () {
      this.index++
      this.numTotal++
      this.endPoint()
    },
    //  la idea es que se lleve un contador para preg correctas y totales, tambien que no deje enviar mas de una respuesta en la misma preg
    increment (isCorrect) {
      if (isCorrect) {
        this.numCorrect++
      }
      //  this.numTotal++;
    },
    //  mensaje final del quiz
    endPoint () {
      if (this.numTotal === this.questions.length + 1) {
        if (this.numCorrect === 10) {
          alert('Congratulations, your number of correct answers is ' + this.numCorrect + ' - Start again')
          this.numCorrect = 0
          this.numTotal = 1
          this.index = 0
        } else {
          alert('Game over! Try again!')
          this.numCorrect = 0
          this.numTotal = 1
          this.index = 0
        }
      }
    }
  },
  //  haciendo promesa para traerme las preguntas de la api de preguntas
  mounted: function () {
    fetch(
      'https://opentdb.com/api.php?amount=10&category=26&difficulty=easy&type=multiple',
      { method: 'get' })
      .then(
        (response) => {
          return response.json()
        })
      .then(
        (jsonData) => {
          this.questions = jsonData.results
        })
  }
}
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
