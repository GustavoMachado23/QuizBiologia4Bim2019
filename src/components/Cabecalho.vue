<template>
    <div>
        <b-nav tabs>
            <b-nav-item disabled><b>Quiz Dahora</b></b-nav-item>
            <b-nav-item disabled v-show="exibirQuestoes">Acertos: {{ numCorrect }} / {{ numTotal }}</b-nav-item>
            <b-nav-item disabled v-show="isLoggedIn">{{ defUsername }}</b-nav-item>
            <div v-show="!exibirQuestoes">
                <b-nav-item v-on:click="trocarNome" v-show="isLoggedIn">Mudar nome</b-nav-item>
                <div v-show="podeCriar">
                    <b-nav-item v-on:click="criarSala" v-show="querCriar">Criar uma sala</b-nav-item>
                    <b-nav-item v-on:click="entrarSala" v-show="!querCriar">Entrar em uma sala</b-nav-item>
                </div>
            </div>
        </b-nav>
    </div>
</template>

<script>
export default {
    data: function() {
        return {
            querCriar: true
        }
    },
    props: [
        'numCorrect',
        'numTotal',
        'exibirQuestoes',
        'defUsername',
        'isLoggedIn',
        'podeCriar'
    ],
    methods: {
        trocarNome: function() {
            this.$emit('trocarNome')
        },
        criarSala: function() {
            this.querCriar = false
            this.$emit('criarSala')
        },
        entrarSala: function() {
            this.querCriar = true
            this.$emit('entrarSala')
        }
    }
}
</script>

<style>
body {
    background-color: white;
}
</style>
