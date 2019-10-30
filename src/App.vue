<template>
  <div id="app">
    <Cabecalho
      :numCorrect="numCorrect"
      :numTotal="numTotal"
      :exibirQuestoes="exibirQuestoes"
      :defUsername="defUsername"
      :isLoggedIn="isLoggedIn"
      :podeCriar="podeCriar"
      @trocarNome="trocarNome"
      @criarSala="criarSala"
      @entrarSala="entrarSala"
    />
    <b-container class="bv-example-row" v-show="!exibirQuestoes">
      <div v-show="!isLoggedIn">
        <div v-show="!createName">
          <LoginForm 
            v-show="doLogin"
            @isLogged="isLogged"
            @clickLogin="clickLogin"
          />
          <RegisterForm 
            v-show="!doLogin"
            @isLogged="isRegistered"
            @clickLogin="clickLogin"
          />
        </div>
        <div v-show="createName">
          <CreateUser
            :userEmail="userEmail"
            :userUID="userUID"
            @sentUsername="sentUsername"
          />
        </div>
      </div>
      <LoggedIn
        v-show="isLoggedIn"
        :defUsername="defUsername"
        :logCriarSala="logCriarSala"
        :userUID="userUID"
        @IdSalaCriadaMethod="IdSalaCriadaMethod"
        @updateGameT="updateGameT"
      />
    </b-container>
    <b-container class="bv-example-row" v-show="exibirQuestoes">
      <div v-show="!fimDeJogo"> 
        <Questoes 
          v-if="questions.length"
          :currentQuestion="questions[index]"
          :next="next"
          :increment="increment"
        />
      </div>
      <div v-show="fimDeJogo">
        <AfterGame
          :venceu="venceu"
          :mensagemAfterGame="mensagemAfterGame"
        />
      </div>
    </b-container>
  </div>
</template>

<script>
import Cabecalho from './components/Cabecalho.vue'
import Questoes from './components/Questoes.vue'
import LoginForm from './components/LoginForm.vue'
import RegisterForm from './components/RegisterForm.vue'
import LoggedIn from './components/LoggedIn.vue'
import CreateUser from './components/CreateUser.vue'
import AfterGame from './components/AfterGame.vue'

// Firebase App (the core Firebase SDK) is always required and
// must be listed before other Firebase SDKs
var firebase = require("firebase/app");

// Add the Firebase products that you want to use
require("firebase/auth");
require("firebase/firestore");

var firebaseConfig = {
  apiKey: "AIzaSyC00oTT5BJ2rJ39TqaCmqr883vm28KBXVs",
  authDomain: "quizbiologia2019.firebaseapp.com",
  databaseURL: "https://quizbiologia2019.firebaseio.com",
  projectId: "quizbiologia2019",
  storageBucket: "quizbiologia2019.appspot.com",
  messagingSenderId: "209806899918",
  appId: "1:209806899918:web:364767788fce8ece9aee9f",
  measurementId: "G-1MY2GH7NTQ"
};
firebase.initializeApp(firebaseConfig);

export default {
  name: 'app',
  components: {
    Cabecalho,
    Questoes,
    LoginForm,
    RegisterForm,
    LoggedIn,
    CreateUser,
    AfterGame
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
      acertos: [],
      exibirQuestoes: false,
      doLogin: true,
      userEmail: 'noone@noone.com',
      userUID: 'NoOne',
      createName: false,
      defUsername: 'NoOne',
      isLoggedIn: false,
      logCriarSala: false,
      podeCriar: false,
      IdSalaCriada: 'NenhumaSalaAinda',
      whichPlayer: 0,
      limiteQuestoes: 20,
      venceu: 0,
      mensagemAfterGame: '',
      fimDeJogo: false
    }
  },
  methods: {
    next() {
      if(this.limiteQuestoes > this.numTotal){
        this.index++
        this.checarFimDeJogo()
      } else{
        this.checarFimDeJogo()
      }
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect = this.numCorrect + 1
        this.acertos.push(true)
      }
      else {
        this.acertos.push(false)
      }
      this.numTotal = this.numTotal + 1
      console.log(this.numTotal)
      console.log(this.numCorrect)
      if(this.whichPlayer == 1){
        let dataP12Serve = {
          p1At: this.numTotal,
          p1Acertos: this.numCorrect
        }
        firebase.firestore().collection('partidas').doc(this.IdSalaCriada).update(dataP12Serve)
      } else if(this.whichPlayer == 2){
        let dataP22Serve = {
          p2At: this.numTotal,
          p2Acertos: this.numCorrect
        }
        firebase.firestore().collection('partidas').doc(this.IdSalaCriada).update(dataP22Serve)
      }
    },
    randomQuestion() {
      let randomQuestion = 1
      randomQuestion = Math.floor(Math.random() * 3 + 1)
      return randomQuestion
    },
    isLogged(exibirQ, email, uid) {
      this.isLoggedIn = exibirQ
      this.userEmail = email
      this.userUID = uid
      firebase.firestore().collection('usuarios').doc(uid).get().then((snapshot) => {
        this.defUsername = snapshot.data().nomeUsuario
        this.podeCriar = snapshot.data().podeCriarSala
      })
    },
    isRegistered(exibirQ, email, uid) {
      this.createName = exibirQ
      this.userEmail = email
      this.userUID = uid
    },
    sentUsername(username) {
      this.defUsername = username
      this.isLoggedIn = true
    },
    clickLogin(login) {
      this.doLogin = login
    },
    trocarNome() {
      this.isLoggedIn = false
      this.createName = true
    },
    criarSala() {
      this.logCriarSala = true
    },
    entrarSala() {
      this.logCriarSala = false
    },
    IdSalaCriadaMethod(id, whichPlayer) {
      this.IdSalaCriada = id
      this.whichPlayer = whichPlayer
    },
    updateGameT() {
      firebase.firestore().collection('partidas').doc(this.IdSalaCriada).onSnapshot(res => {
        if(this.whichPlayer == 1){
          this.numCorrect = res.data().p1Acertos
          this.numTotal = res.data().p1At
        } else if(this.whichPlayer == 2){
          this.numCorrect = res.data().p2Acertos
          this.numTotal = res.data().p2At
        }
        this.limiteQuestoes = res.data().limite
        if(res.data().comecar == true){
          this.exibirQuestoes = true
        }
      })
    },
    checarFimDeJogo() {
      if(this.limiteQuestoes <= this.numTotal){
        firebase.firestore().collection('partidas').doc(this.IdSalaCriada).onSnapshot(res => {
          if(this.whichPlayer == res.data().vencedor){
            this.venceu = 1
            this.mensagemAfterGame = "Parabéns, você venceu essa partida!"
            this.fimDeJogo = true
          } else if(res.data().vencedor == 3){
            this.venceu = 3
            this.mensagemAfterGame = "Empate!"
            this.fimDeJogo = true
          } else if(res.data().vencedor == 0){
            this.venceu = 0
            this.mensagemAfterGame = "Aguarde seu oponente terminar a partida!"
            this.fimDeJogo = true
          } else{
            this.venceu = 2
            this.mensagemAfterGame = "Puts, você perdeu a partida!"
            this.fimDeJogo = true
          }
        })
      }
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
  },
  created() {
    this.updateGameT()
    this.checarFimDeJogo()
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
