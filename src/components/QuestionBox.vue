<template>
  <div>
    <b-jumbotron bg-variant="info" text-variant="white" border-variant="dark">
      <template slot="lead">
    <!--Esta variable es question haciendo referencia al indice como lo hace el v-for cuando recorre-->
        {{ currentQuestion.question | format }}
      </template>
      <hr class="my-4"/>
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click.prevent="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer | format }}
        </b-list-group-item>
      </b-list-group>
<!--el boton se desahibilita si el usuario no marca ninguna preg  -->
      <b-button
        variant="dark"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered">
        Submit
      </b-button>
      <b-button @click="next" variant="dark" :disabled="!answered">
        Next
      </b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data: function () {
    return {
      selectedIndex: null,
      correctIndex: null,
      //  nuevo array mezclador de respuestas
      shuffledAnswers: [],
      //  booleano para saber si fue respondida o no
      answered: false
    }
  },
  computed: {
    answers () {
    // esta función ya no se usa en el código terminado
    // se reemplaza por la función de watcher y el método shuffleAnswers
      const answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },
  // uso un watch para ver el cambio de estos indices
  watch: {
    currentQuestion: {
      immediate: true,
      handler () {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
  },
  //  uso un filters para evitar problemas con algunos caracteres especiales del json, como comillas y 's
  filters: {
    format: function (value) {
      value = value.replace(/&#039;s/g, "'s")
      value = value.replace(/ &quot;/g, ' ')
      value = value.replace(/&quot; /g, ' ')
      value = value.replace(/&quot;/g, ' ')
      return value
    }
  },
  methods: {
    //  este indice me guarda la preg seleccionada
    selectAnswer (index) {
      this.selectedIndex = index
    },
    // chequeo si la respuesta seleccionada es igual a la correcta
    submitAnswer () {
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)
    },
    shuffleAnswers () {
      // tengo tanto resp incorrectas como correctas en una sola matriz
      const answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      //   la matriz de respuestas, usando una funcion de la libreria lodash, shuffle
      this.shuffledAnswers = _.shuffle(answers)
      //  guardo el indice de la respuesta correcta]
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass (index) {
      //  creo un metodo para controlar los colores por clase, blue correcto, rojo incorrecto
      let answerClass = ''
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = 'incorrect'
      }
      return answerClass
    }
  }
}
</script>
<style scoped>
  .question-box{
    margin-top: 20px;
  }
  .list-group{
    margin-bottom:15px;
    color:#3d3d3d;
  }
  .list-group-item:hover{
    background: #EEE;
    cursor:pointer;
  }
  .selected {
  background-color: #9bc2cf;
  }
  .correct {
  background-color: #9bef9b;
  }
  .incorrect {
  background-color:#e55b5b;
  }
  .btn{
    margin: 0 5px;
  }
</style>
