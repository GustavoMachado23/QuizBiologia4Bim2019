<template>
    <div>
        <h1>Seja bem vindo ao Quiz Dahora</h1>
        <h3>Aguarde, seu jogo iniciará em alguns instantes</h3>
        <b-button v-on:click="updateGame">Clique aqui se o jogo estiver demorando</b-button>
    </div>
</template>

<script>
export default {
    methods: {
        updateGame() {
            this.$emit('updateGame')
        }
    }
}
</script>