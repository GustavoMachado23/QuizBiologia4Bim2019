<template>
  <div id="app">
    <Cabecalho
      :numCorrect="numCorrect"
      :numTotal="numTotal"
    />
    <b-container class="bv-example-row">
      <Questoes 
        v-if="questions.length"
        :currentQuestion="questions[index]"
        :next="next"
        :increment="increment"
      />
    </b-container>
  </div>
</template>

<script>
import Cabecalho from './components/Cabecalho.vue'
import Questoes from './components/Questoes.vue'

export default {
  name: 'app',
  components: {
    Cabecalho,
    Questoes
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
      acertos: []
    }
  },
  methods: {
    next() {
      this.index++
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect++
        this.acertos.push(true)
      }
      else {
        this.acertos.push(false)
      }
      this.numTotal++
    },
    randomQuestion() {
      let randomQuestion = 1
      randomQuestion = Math.floor(Math.random() * 3 + 1)
      return randomQuestion
    }
  },
  mounted: function() {
    fetch("/apiQuizBiologia.json", {
      method: 'get'
    })
      .then((response) => {
        return response.json()
      })
      .then((jsonData) => {
        this.questions = jsonData.results
      })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
